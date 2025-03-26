# Problem Statement

Multiple emails from customers/clients that are dealt with different teams based on the context. This solution should enable auto-classification of emails based on the context, so the same can be routed to best suited team for further processing.

**Technology**:

-   Python
-   Anaconda platform

# Solution

1. **Email Receiving**: We have a root node, which acts as the central email receiver in an organization. It is continuously monitored for new emails through the IMAP protocol (which is done in a continuously running python service). It can be any professional email service like Gmail, Outlook, etc.
2. **Distributed Task Queue**: Each email is then sent to a distributed task queue, where it is processed by multiple parallel workers, thus ensuring that the system is scalable and can handle a large number of emails. The system can thus **scale horizontally** very effectively.
3. **Parsing**: The email is parsed and the content is extracted (including attachments!)
4. **Encryption**: The content is encrypted using a powerful cypher.
5. **Sending to Server**: The encrypted content is sent to the server where it is decrypted.
6. **Large Language Model**: The content is passed to our powerful Large Language Model, Mixtal 8x7b which extracts the meaning, context and the sentiment from the email and determines the department/team to which the email should be routed to, including any CCs or BCCs that it deems important.
7. **Summarization**: The email is then summarized by a lighter model, and the summary is sent back to the service.
8. **Message Composition**: The service then composes a new email with the summary (for quick reference) and the sentiment (e.g. Urgent, Complaint, Neutral) and the original email (along with attachments), and sends it back to the root node.
9. **Routing**: The full email is then routed to the respective department/team/person through the SMTP Protocol.
10. **Feedback System**: The system also has a feedback system, where in the case of incorrect routing, the user can provide feedback, which is then used to retrain the model.

# Tech Stack


[<img src = "https://raw.githubusercontent.com/get-icon/geticon/fc0f660daee147afb4a56c64e12bde6486b73e39/icons/python.svg" alt = "Python" height = 60px />](https://www.python.org)
<img width="2" />
[<img src = "https://ollama.com/public/assets/c889cc0d-cb83-4c46-a98e-0d0e273151b9/42f6b28d-9117-48cd-ac0d-44baaf5c178e.png" alt = "Ollama" height = 60px />](https://ollama.com/)
<img width="2" />
[<img src = "https://upload.wikimedia.org/wikipedia/commons/1/19/Celery_logo.png" alt = "Celery" height = 60px />](https://docs.celeryq.dev/en/stable/)
<img width="2" />
[<img src = "https://static-00.iconduck.com/assets.00/rabbitmq-icon-1936x2048-zlik4nni.png" alt = "RabbitMQ" height = 60px />](https://www.rabbitmq.com/)


[<img src = "https://avatars.githubusercontent.com/u/141221163?s=200&v=4" alt = "Qwen" height = 60px />](https://huggingface.co/Qwen)
<img width="2" />
[<img src = "https://images.g2crowd.com/uploads/product/image/social_landscape/social_landscape_5351a237b4bf5239e06d7775686837ea/meta-llama-3.png" alt = "Qwen" height = 60px />](https://llama.com)




# Getting Started

Clone or download the repository and follow the steps below to get started.

## Prerequisites

This project requires a message broker installed on the system. We recommend using [RabbitMQ](https://www.rabbitmq.com/).

If you’re using Ubuntu or Debian install RabbitMQ by executing this command:

```bash
sudo apt-get install rabbitmq-server
```

This project also uses [Ollama](https://ollama.com/) under the hood to utilize the power of large language models. To install Ollama, run:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

We are using the Qwen 4b model for the same. You can use any other model as well, just make sure it has enough context window to understand the email content.

Pull Qwen using:

```bash
ollama pull qwen:4b
```

## Installing Dependencies

1. Prefer installing dependencies in a virtual environment. Create a virtual environment by running:

    ```bash
    python3 -m venv .venv
    ```

2. This project uses [Poetry](https://python-poetry.org/) for dependency management. Make poetry use the newly created virtual environment:

    ```bash
    poetry env use python3
    ```

3. To install the dependencies, run the following command:

    ```bash
    poetry install
    ```

4. Then run the demo app to see the RAG pipeline in action.

    ```bash
    streamlit run src/app.py
    ```

5. Head over to http://localhost:8501 to see the app in action.

    ![alt text](assets/demo-ss.png)

# Working

The service monitors the email inbox and checks every 10 seconds to see if there are any new emails. If there are, it parses them and sends them to the RAG pipeline. The RAG pipeline then classifies the email and outputs the department ID to which the mail should be sent.

![alt text](assets/output.png)

Here, the team id `15` corresponds to the ID of the team in the [Heirarchy JSON file](src/data/rag.json):

```json
{
    "name": "Mergers & Acquisitions",
    "id": 15,
    "description": "The Mergers & Acquisitions team is responsible for identifying and facilitating strategic mergers, acquisitions, and divestitures to support Wells Fargo' growth objectives.",
    "is_leaf": true,
    "children": []
},
```