# **Personal Finance Dashboard**

This project is a lightweight personal finance dashboard built using Python, Streamlit, and the Plaid API, deployed on a DigitalOcean Linux server. It provides live metrics on income, expenses, and financial insights using data fetched from your bank accounts, similar to a "Rocket Money" clone.

## **Features**
- **Real-Time Data:** Fetch live data from your financial accounts (read-only) via the Plaid API.
- **Interactive Dashboard:** Visualize your spending, income, and savings with user-friendly charts.
- **Security First:** Read-only access with encrypted communications to keep your financial data safe.
- **Deployed on DigitalOcean:** Easily accessible via a web interface, hosted on a lightweight DigitalOcean server.

---

## **Table of Contents**
1. [Requirements](#requirements)
2. [Setup](#setup)
3. [Plaid API Integration](#plaid-api-integration)
4. [Running the Dashboard](#running-the-dashboard)
5. [Deployment on DigitalOcean](#deployment-on-digitalocean)
6. [Maintenance & Monitoring](#maintenance-monitoring)
7. [Contributing](#contributing)

---

## **Requirements**

Before starting, make sure you have the following:
- **Python 3.8+**
- **Plaid API Account:** Sign up for a free [Plaid API account](https://plaid.com).
- **Streamlit** for the front-end dashboard.
- **Git** for version control.
- **DigitalOcean Droplet** (optional) to deploy the dashboard.

---

## **Setup**

1. **Clone the Repository**
   ```bash
   git clone https://github.com/yourusername/finance-dashboard.git
   cd finance-dashboard
   ```

2. **Create a Virtual Environment**
   ```bash
   python3 -m venv env
   source env/bin/activate  # On Windows, use `env\Scripts\activate`
   ```

3. **Install the Dependencies**
   ```bash
   pip install -r requirements.txt
   ```
   Ensure the `requirements.txt` contains:
   ```plaintext
   streamlit
   plaid-python
   pandas
   matplotlib
   ```

---

## **Plaid API Integration**

1. **Get API Keys from Plaid**  
   Sign up for a Plaid account and create a new Plaid application. Obtain the following credentials:
   - **Client ID**
   - **Secret**
   - **Public Key**
   
   Set up your environment variables or create a `.env` file to store these credentials securely:
   ```bash
   PLAID_CLIENT_ID=your_client_id
   PLAID_SECRET=your_secret
   PLAID_ENV=sandbox  # Use 'sandbox' for testing, 'production' for live data
   ```

2. **Authenticate with Plaid**  
   In your Python code, add the following to initialize Plaid:
   ```python
   from plaid import Client

   client = Client(client_id='your_client_id',
                   secret='your_secret',
                   environment='sandbox')
   ```

3. **Fetching Transactions**
   Use Plaid's Transactions API to fetch recent transactions and income:
   ```python
   def get_transactions():
       response = client.Transactions.get(access_token, start_date, end_date)
       return response['transactions']
   ```

---

## **Running the Dashboard**

1. **Create the Dashboard Script (`app.py`)**
   Here’s a minimal Streamlit setup:
   ```python
   import streamlit as st
   import pandas as pd
   from plaid import Client

   st.title('Personal Finance Dashboard')

   # Fetch data using Plaid and display as a chart
   transactions = get_transactions()  # Replace with your API function
   df = pd.DataFrame(transactions)

   st.line_chart(df[['date', 'amount']])
   ```

2. **Run the Streamlit App Locally**
   ```bash
   streamlit run app.py
   ```
   This will launch the dashboard on `http://localhost:8501`. Access the dashboard in your browser to view your financial metrics.

---

## **Deployment on DigitalOcean**

1. **Set Up a Droplet**
   - Create a **DigitalOcean** droplet running Ubuntu.
   - SSH into the server and install Python, Pip, and Nginx for the webserver.

2. **Install Python & Streamlit on Server**
   ```bash
   sudo apt update
   sudo apt install python3-pip nginx
   pip3 install streamlit plaid-python pandas
   ```

3. **Configure Nginx**
   - Set up Nginx as a reverse proxy to forward HTTP requests to the Streamlit app.
   - Create a configuration file in `/etc/nginx/sites-available/finance-dashboard`:
     ```
     server {
         listen 80;
         server_name yourdomain.com;

         location / {
             proxy_pass http://localhost:8501;
             proxy_set_header Host $host;
             proxy_set_header X-Real-IP $remote_addr;
             proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
             proxy_set_header X-Forwarded-Proto $scheme;
         }
     }
     ```

   - Enable the site and restart Nginx:
     ```bash
     sudo ln -s /etc/nginx/sites-available/finance-dashboard /etc/nginx/sites-enabled/
     sudo systemctl restart nginx
     ```

4. **Start the Streamlit App on the Server**
   ```bash
   streamlit run app.py --server.port=8501
   ```

---

## **Maintenance & Monitoring**

1. **Unit Tests**
   - Ensure that you have implemented basic tests for the API calls and data processing. You can use `pytest` to automate your tests.
   
   Example:
   ```bash
   pytest tests/test_transactions.py
   ```

2. **Automated Backups**
   - Set up cron jobs or automation scripts to backup transaction data from Plaid to your local SQLite database or another secure storage.

3. **Monitoring**
   - Use services like **UptimeRobot** or **DigitalOcean Monitoring** to keep an eye on the server status and notify you of any downtime.

---

## **Contributing**

Contributions are welcome! If you'd like to contribute:
- Fork the repository.
- Create a new feature branch.
- Submit a pull request with a detailed description of the changes.

---

## **License**

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

This guide should be enough to set up and run the dashboard, and gives others a clear understanding of how to get started.
