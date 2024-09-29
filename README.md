Here's a lab sheet for demonstrating the creation and deployment of an Azure Function using Python. The steps guide you through setting up the function locally, testing it, and deploying it to Azure. 

---

### **Azure Functions with Python - Lab Sheet**

#### **Prerequisites**
- Python 3.6 or higher
- Azure CLI installed
- Azure Functions Core Tools installed
- Visual Studio Code with Azure Functions extension

---

### **Step 1: Install the required tools**

1. **Install Python and Pip:**
   - Ensure you have Python 3.6 or higher installed. You can check by running:
     ```bash
     python --version
     ```

2. **Install Azure CLI:**
   - Follow the [Azure CLI installation guide](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) to install Azure CLI.

3. **Install Azure Functions Core Tools:**
   - Install the Azure Functions Core Tools by running:
     ```bash
     npm install -g azure-functions-core-tools@4 --unsafe-perm true
     ```

4. **Install Visual Studio Code:**
   - Download and install [Visual Studio Code](https://code.visualstudio.com/).

5. **Install Azure Functions Extension:**
   - Open Visual Studio Code, and install the "Azure Functions" extension from the marketplace.

---

### **Step 2: Create a Python Azure Function**

1. **Open Visual Studio Code.**

2. **Create a new project:**
   - Open the Command Palette (`Ctrl+Shift+P`) and type `Azure Functions: Create New Project...`.
   - Select a folder for the project.
   - Choose `Python` as the language.
   - Select the Python version (3.8 or later).
   - Choose `HTTP trigger` as the template for the function.
   - Name the function `HttpTriggerFunction`.
   - Set authorization level to `Anonymous` so that you don't need an API key.

3. **Open the generated Python file:**
   The structure of the function is automatically created. Your function file `__init__.py` will look like this:
   ```python
   import logging
   import azure.functions as func

   def main(req: func.HttpRequest) -> func.HttpResponse:
       logging.info('Python HTTP trigger function processed a request.')

       name = req.params.get('name')
       if not name:
           try:
               req_body = req.get_json()
           except ValueError:
               pass
           else:
               name = req_body.get('name')

       if name:
           return func.HttpResponse(f"Hello, {name}.")
       else:
           return func.HttpResponse(
               "Please pass a name on the query string or in the request body",
               status_code=400
           )
   ```

---

### **Step 3: Run the Function Locally**

1. **Set up a virtual environment:**
   - In the terminal, navigate to your project folder and run:
     ```bash
     python -m venv .env
     source .env/bin/activate  # On Windows use `.env\Scripts\activate`
     ```

2. **Install the dependencies:**
   - Install the required modules by running:
     ```bash
     pip install -r requirements.txt
     ```

3. **Run the Azure Function locally:**
   - Run the function using the Azure Functions Core Tools:
     ```bash
     func start
     ```
   - The local server will start, and the function can be accessed via `http://localhost:7071/api/HttpTriggerFunction`.

4. **Test the function locally:**
   - Open a browser or use curl to test the function.
     ```bash
     curl http://localhost:7071/api/HttpTriggerFunction?name=Azure
     ```
   - You should receive a response: `Hello, Azure.`

---

### **Step 4: Deploy the Function to Azure**

1. **Log in to Azure:**
   - In the terminal, log in to Azure using the Azure CLI:
     ```bash
     az login
     ```

2. **Create a Resource Group:**
   - If you don't have a resource group, create one:
     ```bash
     az group create --name <ResourceGroupName> --location <Location>
     ```

3. **Create a Storage Account:**
   - Create a storage account in the resource group:
     ```bash
     az storage account create --name <StorageAccountName> --resource-group <ResourceGroupName> --location <Location> --sku Standard_LRS
     ```

4. **Create a Function App:**
   - Create a function app in the resource group:
     ```bash
     az functionapp create --resource-group <ResourceGroupName> --consumption-plan-location <Location> --runtime python --runtime-version 3.8 --functions-version 3 --name <FunctionAppName> --storage-account <StorageAccountName>
     ```

5. **Deploy the function:**
   - Deploy your function to Azure using Visual Studio Code or Azure CLI:
     ```bash
     func azure functionapp publish <FunctionAppName>
     ```

---

### **Step 5: Test the Deployed Function**

1. **Access the deployed function:**
   - After deployment, you can access your function using the URL:
     ```
     https://<FunctionAppName>.azurewebsites.net/api/HttpTriggerFunction?name=Azure
     ```

2. **Test the function:**
   - Use a browser or Postman to send a GET request and verify the function works on Azure.

---

### **Step 6: Monitor the Function**

1. **View logs:**
   - You can view the logs of your function using Azure Portal or the Azure CLI:
     ```bash
     az functionapp log tail --name <FunctionAppName> --resource-group <ResourceGroupName>
     ```

2. **Monitor performance:**
   - Use the Azure Portal to monitor the performance and invocations of your function under the "Monitor" section.

---

### **Step 7: Clean Up Resources**

1. **Delete the Resource Group:**
   - To clean up all resources created, delete the resource group:
     ```bash
     az group delete --name <ResourceGroupName> --yes --no-wait
     ```

---

This lab sheet provides an end-to-end demonstration of creating, running, deploying, and monitoring an Azure Function using Python.

