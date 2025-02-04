# Lab 1B: Bluetooth

## Prelab

The objective of this lab is to set up Bluetooth communication between my computer and the Artemis board, and to create a framework for exchanging data between them that can be adapted for future labs.

### Computer Setup
We begin by setting up our computer by completing the following steps.

1. Install Python 3 and pip.

2. Install a virtual environment by running the following commands in Terminal or any available command line interface (CLI). The second command should be run in the desired project directory.

 python3 -m pip install --user virtualenv

 python3 -m venv FastRobots_ble

 3. Use the vitual environment by activating and deactivating it with the following commands:

 source FastRobots_ble/bin/activate

 deactivate

 4. Install bleak Python packages. This must be done while the virtual environment is activated.

  pip install numpy pyyaml colorama nest_asyncio bleak jupyterlab

### Code Base

### Jupyter Server and Board Setup

### Configurations

## Lab Tasks

### Task 1: ECHO command

Thi

### Task 2

### Task 3

### Task 4

### Task 5

### Task 6

### Task 7

### Task 8

### MEng Task 1: Effective Data Rate and Overhead

### MEng Task 2: Reliability


## Discussion

In this lab, I gained a deeper understanding of Bluetooth communication. The Bluetooth libraries simplify its implementation, as they abstract away the underlying complexities. Initially, I struggled to understand the purpose of the notification handler because I was required to collect so little data. Later, I realized it becomes particularly useful when dealing with larger datasets, as it efficiently manages the process of waiting for and responding to incoming data without requiring manual intervention at every step.
