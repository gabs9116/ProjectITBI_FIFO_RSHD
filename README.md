# FIFO Remote Shell Daemon (RSHD) Simulator

A lightweight, multi-process remote shell daemon simulation built in Bash on Linux. The project was developed as a team project for the Basic Technical Tools in Informatics course, focusing on Inter-Process Communication (IPC) using named pipes (FIFOs) instead of network sockets.

## How it works

The system uses a Master-Worker architecture to dispatch and execute shell commands concurrently:

1. **Client (`client.sh`):** Creates its own temporary reply FIFO based on its PID (`/tmp/server-reply-<PID>`), formats the request using a strict protocol (`BEGIN-REQ [PID: command] END-REQ`), and writes it to the central Master FIFO.
2. **Master (`master.sh`):** Continuously reads requests, parses the PID and command using regex (`sed`), and balances the workload across worker processes using a Round-Robin algorithm.
3. **Slaves (`slave.sh`):** Persistent background worker processes. Each slave listens to its own input FIFO, executes the received command using `eval`, redirects standard output and error directly to the client's private reply FIFO, and handles errors gracefully.
4. **Cleanup:** Once the client receives and displays the output, it unlinks its temporary reply FIFO to maintain a clean filesystem.
