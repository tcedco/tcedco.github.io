---
title: "Codects: Blockchain"
date: 2024-08-15
layout: post
outputs: [html, json]
categories: [blockchain]
outgoing: true
---

# Understanding Blockchain with a Simple Python Project

Welcome to the inaugural edition of **Codects** — a series dedicated to exploring exciting coding projects and diving deep into emerging technologies. Each week, I'll be tackling a new project, experimenting with different tech concepts, and sharing my journey along the way. For my very first project, we’re taking a look into the world of **blockchain technology** by building a simple blockchain implementation in Python. Let's dive into what blockchain is, how I built the project, and what I learned along the way.

## What is Blockchain?

Before we get into the project, let’s take a moment to understand the idea behind a blockchain. At its essence, a blockchain is a decentralized digital **ledger** (a book where an organization accounts the amount it spends and receives) that records transactions across a network of computers. Imagine a ledger book where each page (block) contains a record of several transactions, and each page links to the one before it, creating a chain. This structure ensures that once data is recorded, it’s incredibly difficult to change or alter it.

Here are the core concepts of a blockchain:

- **Block**: Each block contains data, a timestamp, a unique identifier (hash), and the hash of the previous block.
- **Hash**: A cryptographic hash is a fixed-size string generated from the block’s data. Any change in data results in a completely different hash, which helps ensure data integrity.
- **Proof of Work**: This is a consensus mechanism that requires solving a computationally difficult problem to add a new block. It’s essential for maintaining the security of the blockchain.

## Diving into the Project

### Designing the Block Structure

Once I had a foundational understanding of blockchain, I set out to build a basic blockchain from scratch. The first step was to design the block structure. Each block in a blockchain contains several essential components: an **index** to identify its position in the chain, a **timestamp** for when it was created, **data** to be recorded, the block’s **hash**, and the **hash of the previous block**.

Designing this structure was straightforward. I knew that each block had to link to the previous one to maintain the chain's integrity. This is what I cooked up:

```python
def create_block(index, previous_hash, timestamp, data, hash):
    return {
        "index": index,
        "previous_hash": previous_hash,
        "timestamp": timestamp,
        "data": data,
        "hash": hash
    }
```

### Hashing Blocks

**Hashing** was the next crucial step. Hashing converts block data into a fixed-size string of characters, which serves as a unique fingerprint for the block. I used the **SHA-256** hash function, a common choice for cryptographic applications due to its security properties. The hashing function ensured that even a tiny change in the block’s content would result in a completely different hash, which is fundamental for detecting tampering. Here's how the hashing function looked:

```python
import hashlib
import json

def hash_block(block):
    return hashlib.sha256(json.dumps(block, sort_keys=True).encode()).hexdigest()
```

### Implementing Proof of Work

One of the more challenging aspects was implementing the **proof of work**. Proof of work is a consensus algorithm that requires solving a computational problem to add a new block to the blockchain. This mechanism helps secure the blockchain by making it costly and time-consuming to add new blocks, thus preventing malicious attacks.

The proof of work function required finding a number that, when hashed, results in a hash with a certain number of leading zeroes. This process is computationally intensive, which deters malicious actors from tampering with the blockchain. Here’s how I implemented it:

```python
def proof_of_work(last_proof):
    proof = 0

    while valid_proof(last_proof, proof) is False:
        proof += 1

    return proof
```

### Adding Data & Mining Blocks

Adding data to the blockchain and mining new blocks were the next steps. I needed functions to handle these tasks effectively. Adding data involved appending it to a list, while mining created a new block and appended it to the chain. The challenge was to ensure that each new block was correctly linked to the previous block and that the blockchain maintained its integrity. With that in mind, here's what I've coded:

```python
def new_data(current_data, data):
    return len(current_data.append(data)) + 1

def new_block(proof, previous_hash, chain, current_data):
    block = create_block(
        index = len(chain) + 1,
        previous_hash = previous_hash or hash_block(chain[-1]),
        timestamp = time(),
        data = current_data,
        proof = proof,

        hash = hash_block(create_block(
            index = len(chain) + 1,
            previous_hash = previous_hash or hash_block(chain[-1]),
            timestamp = time(),
            data = current_data,
            proof = proof,
            hash = ""
        ))
    )

    current_data.clear()
    chain.append(block)
    return block
```

### Creating a Command-Line Interface

To interact with the blockchain, I built a simple command-line interface. This allowed users to add data, mine new blocks, and view the blockchain from the terminal. Building this interface became a rewarding part of the project since this provided a more practical way to test and interact with the blockchain:

```python

if __name__ == "__main__":
    chain = []
    current_data = []

    # Create the genesis block
    new_block(proof=100, previous_hash='1', chain=chain, current_data=current_data)

    print("Welcome to the Blockchain!")

    while True:
        command = input("Enter command (new_data, new_block, quit): ")

        if command == 'new_data':
            data = input("Enter data: ")
            new_data(current_data, data)
            print("Data added!")

        elif command == 'new_block':
            last_block_data = last_block(chain)
            last_proof = last_block_data['proof']
            proof = proof_of_work(last_proof)
            new_block(proof, previous_hash=None, chain=chain, current_data=current_data)
            print("Block added!")

        elif command == 'quit':
            break

        else:
            print("Unknown command")
```

## How was the learning experience?

Embarking on this project had a mix of excitement and challenge. While the theoretical concepts of blockchain were pretty intriguing, translating them into a functional codebase required a ton patience and problem-solving. I faced several hurdles along the way—includes understanding the intricacies of hashing and the proof of work mechanism—but overcoming these challenges was immensely satisfying and in general became relatively easier for me to understand.

## Moving Forward

Check out the project's GitHub repository [here](https://github.com/selsayed25/simple-blockchain-implementation)!

This project marks the beginning of the **Codects** series. Future projects will delve into various aspects of programming and tech offering both insights and practical applications.

If there is a project that you want me to dive into or explore, contact me via Instagram at [@elsayedcodes](https://instagram.com/elsayedcodes), where I'll be posting my projects, research, and development. I'm excited to continue this journey of learning and discovery, and I hope you'll join me in the adventures ahead!