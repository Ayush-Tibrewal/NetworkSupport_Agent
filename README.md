# Network Support Agent Chatbot

This project implements a customer support agent chatbot that integrates data from a MongoDB network database, a PostgreSQL ticketing system, and leverages a large language model (LLM) for natural language processing. The workflow uses a state graph to route requests through different processing steps, including:

- **Network Data Fetching:** Querying a MongoDB collection for network status.
- **Network Analysis:** Determining if the network is working based on the fetched data.
- **Ticketing:** Creating or updating tickets in a PostgreSQL database when network issues are detected.
- **Customer Support Interaction:** Generating a final natural language response with LLM assistance.

The system uses [langgraph](https://github.com/) to model and compile the state-based workflow and [ChatGroq](https://github.com/) as the LLM for generating responses.

---

## Table of Contents

- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Workflow Overview](#workflow-overview)
- [Code Structure](#code-structure)
- [License](#license)

---

## Features

- **Multi-step Workflow:** Integrates network data lookup, issue analysis, and ticket management.
- **Dynamic Routing:** Uses conditional edges to decide whether to escalate an issue to ticketing.
- **LLM Integration:** Uses a language model to generate natural language responses based on query context and history.
- **Chat History Maintenance:** Maintains conversation history for contextual responses.
- **Database Integration:** Connects to both MongoDB (for network details) and PostgreSQL (for ticketing).

---

## Prerequisites

- **Python 3.8+**  
- **MongoDB:** A running MongoDB instance with a database named `local` and collection `networks4`.
- **PostgreSQL:** A PostgreSQL database with a table `tickets`. The table should include at least the following columns:
  - `ticket_id` (Primary Key)
  - `customer_id`
  - `issue_type`
  - `location`
  - `status`
  - `created_at`
  - `updated_at`
- **Environment Variables:**  
  - `GROQ_API_KEY` – API key for the ChatGroq LLM.

---

## Installation




   *Example packages might include:*

   - `langgraph`
   - `langchain_groq`
   - `pymongo`
   - `psycopg2`
   - `IPython`
   - `typing_extensions`

---

## Configuration

1. **MongoDB Connection:**

   The code connects to MongoDB at:
   ```python
   client = MongoClient("mongodb://localhost:27017/")
   db = client["local"]
   networks_collection = db["networks4"]
   ```
   Adjust the URI and database/collection names as needed.

2. **PostgreSQL Connection:**

   The PostgreSQL connection string is defined as:
   ```python
   conn = psycopg2.connect(
       "postgresql://neondb_owner:npg_9yG5HNcVhlto@ep-green-feather-a894p77b-pooler.eastus2.azure.neon.tech/neondb?sslmode=require"
   )
   ```
   Update the credentials and connection string to match your environment.

3. **LLM API Key:**

   Set your ChatGroq API key as an environment variable:
   ```bash
   export GROQ_API_KEY=your_actual_api_key
   ```
   Or modify the code to use your key directly (not recommended for production).

---

## Usage

Run the main script to start the chatbot. For example, if your main file is named `app.py`:

```bash
python app.py
```

### Example Interaction

```plaintext
You: Network outage at Building A
Bot: 
Network details for Building A:
{'location': 'Building A', 'status': 'not working', ...}
New Ticket Created - ID: 12345 - Status: Open - Created At: 2025-02-07 15:30:00
Final response:
...
```

Type `exit` or `quit` to stop the chatbot.

---

## Workflow Overview

1. **Network Data Fetcher:**  
   - Processes the user query and searches MongoDB for matching network location and status.
   - Populates the state with the query result.

2. **Network Expert:**  
   - Analyzes network data and decides if the network is working.
   - If issues are detected, the workflow routes to the ticketing expert.

3. **Ticketing Expert:**  
   - Checks the PostgreSQL database for an existing ticket.
   - Updates an existing ticket or creates a new one based on the issue details.

4. **Customer Support Agent:**  
   - Generates the final response using the LLM, taking into account network details, ticket information, and chat history.

The state graph routes through these nodes and uses conditional logic to determine the next step based on the network analysis results.

---

## Code Structure

- **Imports:**  
  Imports necessary modules and libraries for database connectivity, LLM integration, and state management.

- **Database Setup:**  
  Establishes connections to MongoDB and PostgreSQL.

- **LLM Initialization:**  
  Configures the ChatGroq LLM using an API key.

- **State Definition:**  
  Uses a `TypedDict` (`ChatState`) to define the expected structure of the state passed between nodes.

- **Workflow Functions:**  
  - `networkdata_fetcher(state: ChatState)`: Queries the MongoDB network collection.
  - `network_expert(state: ChatState)`: Uses the LLM to analyze the fetched network data.
  - `ticketing_expert(state: ChatState)`: Interacts with the PostgreSQL database to handle ticket creation or updates.
  - `customer_support_agent(state: ChatState)`: Uses the LLM to generate the final response based on the overall state.

- **Workflow Construction:**  
  Builds a state graph with nodes and conditional edges to route the flow of execution.

- **Main Loop:**  
  The script enters a loop to continuously accept user input, process the state through the workflow, and display the bot’s response.

---

## License

*Include your license information here (e.g., MIT, Apache 2.0, etc.)*

---

Feel free to contribute or open issues if you find bugs or have suggestions for improvements.

Happy coding!
