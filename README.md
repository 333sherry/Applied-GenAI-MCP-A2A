# Applied-GenAI-MCP-A2A

# Multi-Agent Customer Service System (MCP + A2A)

This repository contains the implementation for Assignment 5 — Applied GenAI, where I built a fully functional multi-agent customer service system using:

* **Model Context Protocol (MCP)** for database access
* **Google ADK A2A (Agent-to-Agent)** for multi-agent coordination
* **LLM-powered agents** for routing, support, and data retrieval
* **SQLite** database for persistent customer and ticket data

The full system runs end-to-end inside a single notebook.

---

# Repository Structure

```
Assignment_5_applied_genai.ipynb     ← Main notebook (MCP server + agents + tests)
database_setup.py                    ← Builds the SQLite DB with schema + triggers
support.db                           ← SQLite database used by MCP server
README.md                            ← Project documentation
requirements.txt                     ← Required Python packages
```

---

# Project Overview

This project implements a three-agent system:

### Router Agent (Orchestrator)

* Receives customer queries
* Performs intent detection
* Delegates tasks to specialist agents
* Handles multi-intent and multi-step workflows
* Combines outputs into a final response

### Customer Data Agent (MCP-Backed Specialist)

* Calls MCP tools to interact with the SQLite database
* Fetches customer data
* Updates records
* Creates tickets
* Retrieves customer history

### Support Agent (Customer Service Specialist)

* Handles troubleshooting and support requests
* Requests customer context from the Data Agent
* Performs escalation through ticket creation
* Generates natural-language responses

All agents communicate using the A2A protocol and run on lightweight local servers.

---

# MCP Server

The MCP server exposes all required tools defined in the assignment:

### Required Tools Implemented

| Tool                                          | Description                         |
| --------------------------------------------- | ----------------------------------- |
| `get_customer(customer_id)`                   | Fetch a single customer             |
| `list_customers(status, limit)`               | Filter and list customers           |
| `update_customer(customer_id, data)`          | Modify customer fields              |
| `create_ticket(customer_id, issue, priority)` | Open support tickets                |
| `get_customer_history(customer_id)`           | Retrieve all tickets for a customer |

The server communicates using JSON-RPC + Server-Sent Events.

`database_setup.py` creates:

* `customers` table
* `tickets` table
* Triggers to auto-update timestamps
* Sample records

`support.db` is the database used by the agents.

---

# Running the Project (Colab)

The entire project runs inside:

```
Assignment_5_applied_genai.ipynb
```

To run:

1. Open the notebook in Google Colab
2. Run each section sequentially:

   * Initialize database
   * Start MCP server
   * Launch A2A agent servers
   * Execute router-agent tests
3. Observe agent-to-agent communication in logs
4. Review MCP tool output and multi-step coordination

No additional setup is required outside Colab.

---

# Test Scenarios Implemented

All required scenarios are included and executed in the notebook.

### **Scenario 1 — Task Allocation**

"I need help with my account, customer ID 12345"

### **Scenario 2 — Negotiation / Escalation**

"I want to cancel my subscription but I'm having billing issues"

### **Scenario 3 — Multi-Step Coordination**

"What's the status of all high-priority tickets for premium customers?"

### **Required Test Queries**

✔ Coordinated Query:
`I'm customer 12345 and need help upgrading my account`

✔ Complex Query:
`Show me all active customers who have open tickets`

✔ Escalation:
`I've been charged twice, please refund immediately!`

All scenario outputs are captured in the notebook.

---

# Environment & Dependencies

If running locally, install:

```
pip install flask flask-cors pyngrok requests nest_asyncio uvicorn httpx google-genai google-colab google-adk
```

(Dependencies are installed directly in the notebook when running in Colab, so no manual setup is required.)

---

# Conclusion

This project strengthened my understanding of multi-agent orchestration, MCP-based tool integration, and database-backed LLM systems. The main challenges included debugging asynchronous A2A interactions, coordinating multiple agent servers, and ensuring correct tool routing through the Router Agent. Building the MCP server deepened my understanding of how LLMs can interact with structured data reliably without hallucination. Overall, this hands-on implementation demonstrated how multi-agent systems can provide scalable, intelligent automation for customer service.
