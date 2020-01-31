---
layout: article
title: 区块链 — 原理与代码实现
tags: 区块链

lang: zh-Hans
key: BlockChain_Principle_And_Implement
pageview: true
toc: true
show_subscribe: false
---

```python
import hashlib
import json
from uuid import uuid4
import requests
from time import time
from urllib.parse import urlparse
from flask import Flask, jsonify, request
from argparse import ArgumentParser


class BlockChain:

    def __init__(self):
        self.chain = []
        self.current_transactions = []
        self.nodes = set()

        # 创世块
        self.new_block(proof=100, previous_hash=1)

    # 注册节点
    def register_node(self, address: str):
        parsed_url = urlparse(address)
        self.nodes.add(parsed_url.netloc)

    # 判断是否为有效链
    def valid_chain(self, chain) -> bool:

        last_block = chain[0]
        current_index = 1

        while current_index < len(chain):
            block = chain[current_index]

            # 计算出的 hash 值与实际不符
            if block['previous_hash'] != self.hash(last_block):
                return False

            # 是否满足工作量证明
            if not self.valid_proof(last_block['proof'], block['proof']):
                return False

            last_block = block
            current_index += 1

        return True

    # 解决冲突，选择最长的链
    def resolve_conflicts(self) -> bool:

        neighbours = self.nodes

        max_length = len(self.chain)
        new_chain = None

        for node in neighbours:
            reponse = requests.get(f'http://{node}/chain')
            if reponse.status_code == 200:
                length = reponse.json()['length']
                chain = reponse.json()['chain']

                if length > max_length and self.valid_chain(chain):
                    max_length = length
                    new_chain = chain

        if new_chain:
            self.chain = new_chain
            return True

        return False

    # 创建区块
    def new_block(self, proof, previous_hash=None):

        block = {
            'index': len(self.chain) + 1,
            'timestamp': time(),
            'transcations': self.current_transactions,
            'proof': proof,
            'previous_hash': previous_hash or self.hash(self.last_lock)
        }

        self.current_transactions = []
        self.chain.append(block)

        return block

    # 新建交易
    def new_transaction(self, sender, recipient, amount) -> int:
        self.current_transactions.append(
            {
                'sender': sender,
                'recipient': recipient,
                'amount': amount
            }
        )
        return self.last_lock['index'] + 1

    @staticmethod
    def hash(block):
        block_string = json.dumps(block, sort_keys=True).encode()
        return hashlib.sha256(block_string).hexdigest()

    # 返回最后一个区块
    @property
    def last_lock(self):
        return self.chain[-1]

    # 工作量证明
    def proof_of_work(self, last_proof: int) -> int:
        proof = 0
        while self.valid_proof(last_proof, proof) is False:
            proof += 1
        return proof

    # hash 值前 4 位是否为 0
    def valid_proof(self, last_proof: int, proof: int) -> bool:
        guess = f'{last_proof}{proof}'.encode()
        guess_hash = hashlib.sha256(guess).hexdigest()
        return guess_hash[0:4] == '0000'


app = Flask(__name__)
blockchain = BlockChain()

node_identifier = str(uuid4()).replace('-', '')


@app.route('/transactions/new', methods=['POST'])
def new_transaction():
    values = request.get_json()
    required = ['sender', 'recipient', 'amount']
    if values is None or not all(k in values for k in required):
        return 'Missing values', 400

    index = blockchain.new_transaction(values['sender'],
                                       values['recipient'],
                                       values['amount'])

    response = {"message": f'Transcation will be added to Block {index}'}
    return jsonify(response), 201


@app.route('/mine', methods=['GET'])
def mine():
    last_block = blockchain.last_lock
    last_proof = last_block['proof']
    proof = blockchain.proof_of_work(last_proof)

    blockchain.new_transaction(sender="0",
                               recipient=node_identifier,
                               amount=1)
    block = blockchain.new_block(proof, None)

    response = {
        "message": "New Block Forged",
        "index": block['index'],
        "transcations": block['transcations'],
        "proof": block['proof'],
        "previous_hash": block['previous_hash']
    }

    return jsonify(response), 200


@app.route('/chain', methods=['GET'])
def full_chain():

    response = {
        'chain': blockchain.chain,
        'length': len(blockchain.chain)
    }
    return jsonify(response), 200


# {"nodes": ["http://127.0.0.1:5001"]}
@app.route('/nodes/register', methods=['POST'])
def register_nodes():
    values = request.get_json()

    nodes = values.get('nodes')

    if nodes is None:
        return "Error: please supply a valid list of nodes", 400

    for node in nodes:
        blockchain.register_node(node)

    response = {
        "message": "New nodes has been added",
        "total_nodes": list(blockchain.nodes)
    }

    return jsonify(response), 201

# 解决冲突
@app.route('/nodes/resolve', methods=['GET'])
def consensus():
    replaced = blockchain.resolve_conflicts()

    if replaced:
        response = {
            "message": "Our chain was replaced",
            "new_chain": blockchain.chain
        }
    else:
        response = {
            "message": "Our chain is authoritative",
            "chain": blockchain.chain
        }

    return jsonify(response), 200


if __name__ == '__main__':

    parser = ArgumentParser()
    parser.add_argument('-p', '--port', default=5000, type=int, help='port to listen to')
    args = parser.parse_args()
    port = args.port

    app.run(host='0.0.0.0', port=port)
```