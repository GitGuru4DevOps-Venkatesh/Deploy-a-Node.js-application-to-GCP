# Deploy-a-Node.js-application-to-GCP
To create a GitHub Actions workflow file that deploys a Node.js application to Google Cloud Platform (GCP) using Terraform, you'll need to define the necessary steps and environment variables in a YAML file. Below is an example workflow file for this purpose:

![image](https://github.com/GitGuru4DevOps-Venkatesh/Deploy-a-Node.js-application-to-GCP/assets/128009454/717885c0-31cc-454c-ad9b-1192944541d3)

```yaml
name: Deploy Node.js App to GCP with Terraform

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Node.js
        uses: actions/setup-node@v2
        with:
          node-version: 14

      - name: Install dependencies
        run: npm install

      - name: Authenticate with GCP
        uses: GoogleCloudPlatform/github-actions/setup-gcloud@v0.2
        with:
          project_id: ${{ secrets.GCP_PROJECT_ID }}
          service_account_key: ${{ secrets.GCP_SA_KEY }}
          export_default_credentials: true

      - name: Terraform Init
        run: terraform init
        working-directory: infra # Change to your Terraform code directory

      - name: Terraform Apply
        run: terraform apply -auto-approve
        working-directory: infra # Change to your Terraform code directory

      - name: Deploy Node.js App
        run: |
          # Replace these commands with your actual deployment steps
          # e.g., copy your Node.js app files to a GCP bucket, deploy to App Engine, etc.
          echo "Deploying Node.js App to GCP..."
        env:
          GCP_PROJECT_ID: ${{ secrets.GCP_PROJECT_ID }}

      - name: Terraform Destroy
        run: terraform destroy -auto-approve
        working-directory: infra # Change to your Terraform code directory
```

In this example:

1. The workflow is triggered on pushes to the "main" branch.

2. It checks out your code repository, sets up Node.js, installs dependencies, and authenticates with GCP using a service account key and project ID stored as GitHub secrets.

3. It initializes Terraform and applies the infrastructure changes (deployment) in the "infra" directory.

4. It deploys your Node.js application to GCP. You should replace the comment with actual deployment steps, which may involve uploading application files to a GCP bucket or deploying to a specific GCP service like App Engine, Kubernetes Engine, or Cloud Run.

5. Finally, it includes a Terraform destroy step. Be cautious with this step, as it will destroy the GCP resources created during the apply step.

Make sure to replace the placeholders like `${{ secrets.GCP_PROJECT_ID }}` and `${{ secrets.GCP_SA_KEY }}` with your actual secrets and adjust the Terraform working directory ("infra" in this example) to match your project's directory structure.

This is a basic example, and your specific deployment process may vary depending on your Node.js application and GCP resources.
===========================================================================================================================================================================
====
I'll elaborate on the steps needed to set up a GitHub Actions workflow to deploy a Node.js application to Google Cloud Platform (GCP) using Terraform in real-time. This guide assumes you have a Node.js application, a GCP account, and a Terraform configuration ready. If not, create your Node.js app and set up the necessary GCP resources before proceeding.
====
Step 1: Set Up GitHub Repository
====
1. Create a new GitHub repository or use an existing one to host your Node.js application code and Terraform configuration.

Step 2: Set Up Google Cloud Service Account

1. In the Google Cloud Console, create a service account with appropriate roles and permissions for the resources you intend to create. For simplicity, you can give the service account the "Editor" role, but for security reasons, consider granting only the necessary permissions.

2. Download the service account's JSON key file and store it securely.

Step 3: Set Up GitHub Secrets

1. In your GitHub repository, go to "Settings" > "Secrets" and add the following secrets:
   - `GCP_PROJECT_ID`: Set this to your GCP project ID.
   - `GCP_SA_KEY`: Paste the contents of the service account JSON key file you downloaded.

Step 4: Create Terraform Configuration

1. Create a directory in your repository for your Terraform configuration, for example, "terraform."

2. Create your Terraform configuration files, including `.tf` files and `main.tf` or any other entry point file.

3. In your Terraform configuration, set up resources such as Google Cloud Storage buckets, Cloud Functions, App Engine, or whatever you need for your Node.js application.

4. In your Terraform configuration, reference the project and credentials using data sources, such as:

   ```hcl
   provider "google" {
     project = data.google_project.default.project
   }

   data "google_project" "default" {}
   ```

Step 5: Create a GitHub Actions Workflow

1. In your repository, create a directory called `.github/workflows`.

2. Inside the `workflows` directory, create a YAML file (e.g., `deploy.yml`) with the following content:

```yaml
name: Deploy Node.js App to GCP with Terraform

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Node.js
        uses: actions/setup-node@v2
        with:
          node-version: 14

      - name: Install dependencies
        run: npm install

      - name: Authenticate with GCP
        uses: GoogleCloudPlatform/github-actions/setup-gcloud@v0.2
        with:
          project_id: ${{ secrets.GCP_PROJECT_ID }}
          service_account_key: ${{ secrets.GCP_SA_KEY }}
          export_default_credentials: true

      - name: Terraform Init
        run: terraform init
        working-directory: terraform

      - name: Terraform Apply
        run: terraform apply -auto-approve
        working-directory: terraform

      - name: Deploy Node.js App
        run: |
          # Replace these commands with your actual deployment steps
          # e.g., copy your Node.js app files to a GCP bucket, deploy to App Engine, etc.
          echo "Deploying Node.js App to GCP..."
        env:
          GCP_PROJECT_ID: ${{ secrets.GCP_PROJECT_ID }}

      - name: Terraform Destroy
        run: terraform destroy -auto-approve
        working-directory: terraform
```

This workflow file defines the deployment steps as described in the previous response.

Step 6: Commit and Push to GitHub

1. Commit the changes to your repository, including the GitHub Actions workflow file.

2. Push the changes to your repository's main branch.

Step 7: Monitor the Workflow

Once you push the changes, the GitHub Actions workflow will be triggered automatically. You can monitor the progress and any potential errors in the "Actions" tab of your GitHub repository.

Please adapt this example to your specific project structure, Terraform configurations, and deployment steps. It's essential to follow best practices for securing and managing secrets and credentials in your workflow.
===============================================================================================================================================================================================================================
If you don't have a Node.js application and Terraform configuration ready, you'll need to create those components before setting up the GitHub Actions workflow for deployment. Below are the steps to create a simple Node.js application and a basic Terraform configuration to get you started:

**Step 1: Create a Simple Node.js Application**

1. Create a new directory for your Node.js application:

   ```bash
   mkdir my-node-app
   cd my-node-app
   ```

2. Initialize a new Node.js project:

   ```bash
   npm init -y
   ```

3. Create a basic Node.js application file, e.g., `app.js`:

   ```javascript
   const http = require('http');

   const server = http.createServer((req, res) => {
     res.writeHead(200, { 'Content-Type': 'text/plain' });
     res.end('Hello, World!\n');
   });

   const port = process.env.PORT || 3000;

   server.listen(port, () => {
     console.log(`Server running on http://localhost:${port}`);
   });
   ```

4. Add a simple `package.json` script for running the application:

   ```json
   "scripts": {
     "start": "node app.js"
   }
   ```

5. Install any required Node.js dependencies. For this simple example, there are no additional dependencies.

6. Commit your Node.js application to your GitHub repository.

**Step 2: Create a Basic Terraform Configuration**

1. Create a directory for your Terraform configuration:

   ```bash
   mkdir my-terraform-config
   cd my-terraform-config
   ```

2. Create a basic Terraform configuration file, e.g., `main.tf`:

   ```hcl
   provider "google" {
     credentials = file("path/to/your/gcp-credentials.json")
     project    = "your-gcp-project-id"
     region     = "us-central1"
   }

   resource "google_compute_instance" "example" {
     name         = "example-instance"
     machine_type = "f1-micro"
     zone         = "us-central1-a"

     boot_disk {
       initialize_params {
         image = "debian-cloud/debian-9"
       }
     }

     network_interface {
       network = "default"
     }
   }
   ```

   Replace `"path/to/your/gcp-credentials.json"` with the actual path to your GCP service account key file.

3. Initialize your Terraform configuration:

   ```bash
   terraform init
   ```

4. Create a `variables.tf` file if you need to define variables for your Terraform configuration.

5. Commit your Terraform configuration to your GitHub repository.

**Step 3: Set Up GitHub Secrets**

1. In your GitHub repository, go to "Settings" > "Secrets" and add the following secrets:
   - `GCP_PROJECT_ID`: Set this to your GCP project ID.
   - `GCP_SA_KEY`: Paste the contents of the service account JSON key file.

**Step 4: Create and Configure the GitHub Actions Workflow**

Follow the steps mentioned in the previous response to create and configure the GitHub Actions workflow using the sample workflow YAML. Make sure to adjust the workflow to fit your specific project structure, Node.js application, and Terraform configuration.

Once you have completed these steps, your GitHub Actions workflow will be ready to deploy your Node.js application and infrastructure to GCP using Terraform.
