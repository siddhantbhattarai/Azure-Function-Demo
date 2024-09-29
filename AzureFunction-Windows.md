### **Azure Functions with Node.js - Lab Sheet**

#### **Prerequisites**

- **Node.js** installed (version 18 or higher)
- **Azure CLI** installed
- **Azure Functions Core Tools** installed
- **Visual Studio Code** with Azure Functions extension
- **Azure Subscription**

---

### **Step 1: Install Required Tools**

1. **Install Node.js and npm:**
   - Download and install Node.js (which includes npm) from [here](https://nodejs.org/).
   - Verify installation by running:
     ```bash
     node --version
     npm --version
     ```

2. **Install Azure CLI:**
   - Follow the [Azure CLI installation guide](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

3. **Install Azure Functions Core Tools:**
   - Install the Azure Functions Core Tools to develop and test functions locally:
     ```bash
     npm install -g azure-functions-core-tools@4 --unsafe-perm true
     ```

4. **Install Visual Studio Code:**
   - Download and install [Visual Studio Code](https://code.visualstudio.com/).

5. **Install Azure Functions Extension:**
   - Open Visual Studio Code and install the "Azure Functions" extension from the marketplace.

---

### **Step 2: Create a Node.js Azure Function**

1. **Open Visual Studio Code.**

2. **Create a new project:**
   - Open the Command Palette (`Ctrl+Shift+P`) and type `Azure Functions: Create New Project...`.
   - Select a folder for the project.
   - Choose `JavaScript` as the language.
   - Select `HTTP trigger` as the template for the function.
   - Name the function `HttpTriggerFunction`.
   - Set authorization level to `Anonymous`.

3. **View the Generated Code:**
   - Your function file `index.js` will look like this:
     ```javascript
     module.exports = async function (context, req) {
         context.log('JavaScript HTTP trigger function processed a request.');

         const name = (req.query.name || (req.body && req.body.name));
         if (name) {
             context.res = {
                 // status: 200, /* Defaults to 200 */
                 body: "Hello, " + name
             };
         }
         else {
             context.res = {
                 status: 400,
                 body: "Please pass a name on the query string or in the request body"
             };
         }
     };
     ```

---

### **Step 3: Run the Function Locally**

1. **Install Dependencies:**
   - Navigate to the project folder and install dependencies:
     ```bash
     npm install
     ```

2. **Run the Azure Function Locally:**
   - Start the function locally using Azure Functions Core Tools:
     ```bash
     func start
     ```
   - The local server will start, and the function can be accessed via `http://localhost:7071/api/HttpTriggerFunction`.

3. **Test the Function:**
   - Open a browser or use curl to test the function:
     ```bash
     curl http://localhost:7071/api/HttpTriggerFunction?name=Azure
     ```
   - You should receive a response: `Hello, Azure`.

---


### **Step 4: Deploy the Function to Azure with Resource Names**

1. **Log in to Azure:**

   ```bash
   az login
   ```

2. **Create a Resource Group:**

   ```bash
   az group create --name FunctionAppResourceGroup --location eastus
   ```

   - **Resource Group Name**: `FunctionAppResourceGroup`
   - **Location**: `eastus` (You can choose another Azure region if preferred)

3. **Create a Storage Account:**

   ```bash
   az storage account create --name functionappstoragexyz --resource-group FunctionAppResourceGroup --location eastus --sku Standard_LRS
   ```

   - **Storage Account Name**: `functionappstoragexyz`
     - **Note**: The storage account name must be globally unique and can only contain lowercase letters and numbers. Replace `xyz` with a unique combination to ensure uniqueness.
   - **SKU**: `Standard_LRS` (Locally-redundant storage)

4. **Create a Function App:**

   ```bash
   az functionapp create --resource-group FunctionAppResourceGroup --consumption-plan-location eastus --runtime node --functions-version 3 --name MyFunctionAppXYZ --storage-account functionappstoragexyz
   ```

   - **Function App Name**: `MyFunctionAppXYZ`
     - **Note**: The function app name must be globally unique as it forms part of the URL. Replace `XYZ` with a unique combination.
   - **Runtime**: `node` (since you're using Node.js)
   - **Functions Version**: `3` (You can use the latest version available)

5. **Deploy the Function:**

   ```bash
   func azure functionapp publish MyFunctionAppXYZ
   ```

---

### **Step 5: Test the Deployed Function**

1. **Access the Function:**

   - URL:

     ```
     https://MyFunctionAppXYZ.azurewebsites.net/api/HttpTriggerFunction?name=Azure
     ```

     - Replace `MyFunctionAppXYZ` with your actual Function App name.

2. **Test the Function:**

   - Use a browser or a tool like Postman to send a GET request to the URL.
   - You should receive a response:

     ```
     Hello, Azure
     ```

---

### **Explanation of Resource Names and Requirements**

- **Resource Group Name (`FunctionAppResourceGroup`):**

  - Can contain alphanumeric characters, periods, underscores, hyphens, and parentheses.
  - Should be descriptive to easily identify the resources it contains.

- **Storage Account Name (`functionappstoragexyz`):**

  - Must be between 3 and 24 characters in length.
  - Can only contain lowercase letters and numbers.
  - Must be globally unique across all of Azure.
  - **Tip:** Use a combination of letters and numbers for `xyz` to ensure uniqueness, such as `functionappstorage123`.

- **Function App Name (`MyFunctionAppXYZ`):**

  - Must be between 1 and 60 characters.
  - Can contain letters, numbers, and hyphens.
  - Must be globally unique because it forms part of your function's URL.
  - **Tip:** Append a unique suffix like your initials and a number, e.g., `MyFunctionAppABC123`.

---

### **Additional Notes**

- **Choosing Unique Names:**

  - Since both the **Storage Account** and **Function App** names must be globally unique, it's common to append random or unique identifiers.
  - You can generate a unique suffix using a tool or by combining elements like the date, time, or a GUID.

- **Azure Regions:**

  - The `--location` or `--consumption-plan-location` parameter specifies the Azure region where your resources will be deployed.
  - Common regions include `eastus`, `westus`, `westeurope`, etc.
  - Choose a region closest to your users or your data.

---

### **Example with Filled Values**

Assuming your unique suffix is `abc123`, here's how the commands would look:

1. **Create Resource Group:**

   ```bash
   az group create --name FunctionAppResourceGroup --location eastus
   ```

2. **Create Storage Account:**

   ```bash
   az storage account create --name functionappstorageabc123 --resource-group FunctionAppResourceGroup --location eastus --sku Standard_LRS
   ```

3. **Create Function App:**

   ```bash
   az functionapp create --resource-group FunctionAppResourceGroup --consumption-plan-location eastus --runtime node --functions-version 3 --name MyFunctionAppABC123 --storage-account functionappstorageabc123
   ```

4. **Deploy Function:**

   ```bash
   func azure functionapp publish MyFunctionAppABC123
   ```

5. **Access the Function:**

   ```
   https://MyFunctionAppABC123.azurewebsites.net/api/HttpTriggerFunction?name=Azure
   ```

---

### **Final Tips**

- **Consistency:** Keep your resource names consistent and descriptive to make management easier.
- **Security:** Be cautious not to include sensitive information in your resource names, as some of them may be exposed in URLs or logs.
- **Cleanup:** Remember to delete the resource group when you're done to avoid incurring charges:

  ```bash
  az group delete --name FunctionAppResourceGroup --yes --no-wait
  ```

---

By following these updated steps with the provided resource names, you should be able to deploy your Azure Function smoothly.
