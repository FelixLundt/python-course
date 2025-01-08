# Using Google Cloud Platform

**Disclaimer:** I haven't used the Google Cloud Platform much so far, so most of this Guide is LLM-generated, with careful editing. Treat this more as a hint about how to do things, and make sure you know what you're doing yourself! Don't just copy commands and code from this document.

**Warning:** GPU instances can be very expensive! Use your resources wisely and always monitor your billing! I don't know what happens when you exceed your credit limit! Check out the budget tools you can use to limit your spending [here](https://cloud.google.com/billing/docs/how-to/budgets-programmatic-notifications).

## Interacting with Google Cloud Platform

There are several ways to interact with Google Cloud Platform:

1. **GCP Console (Web Interface)**
   - Good for:
     - Initial setup and project creation
     - Managing team members and permissions
     - Monitoring costs and usage
     - Creating Vertex AI notebooks
     - Visual exploration of storage buckets
   - Access via [console.cloud.google.com](https://console.cloud.google.com)

2. **Command Line Interface (CLI)**
   - Good for:
     - Quick file operations with storage
     - Automation and scripting
     - Project configuration
     - Authentication
   - Uses `gcloud` for general GCP operations
   - Uses `gsutil` specifically for storage operations

3. **Python API**
   - Good for:
     - Programmatic access to storage
     - Integration with your training code
     - Automated model checkpointing
     - Data pipeline integration
   - Main package: `google-cloud-storage`
   - Used in notebooks and scripts


## Cloud/local interactions

Here's a schematic overview of cloud/local interactions for neural network training:

Local Development & Testing

1. Train (smaller) models/generate (smaller) data sets locally
   Local Files: model.pt, training_data.npz
2. Test code in cloud (or rather, test cloud with (locally developed) code)


Cloud Compute & Storage

1. Train larger models on powerful GPU instances
2. Generate a lot of data
3. Save checkpoints, trained models and generated data

Using Trained Models

1. Download from cloud, use locally
2. Use in cloud:

   - Option 1: Load local copy
   - Option 2: Load directly from cloud

Key Points:

- Local development for quick iterations
- Cloud for serious training
- Models can be used either locally or loaded directly from cloud
- Cloud Storage acts as central repository for trained models

## Quick Start

### Step-by-step Guide

#### One-time setup
1. Activate your GCP credits
2. Install Google Cloud SDK locally
3. Set up Group/Project
4. Set up Storage

#### Usage
5. Develop Locally
6. Cloud Training/Data Generation
    - Google colab could be a good intermediate step 
        - Free alternative for GPU access
        - Can connect to your GCP storage
        - Cannot use GCP credits for compute
        - Good for development, not for large-scale training
    - Once ready for serious training on GCP, upload what you need (training data, models, ..)
    - Vertex AI Notebooks are probably the easiest way to run the training (like Jupyter notebooks but on GCP)

7. Using Trained Models
    - Either download model locally
    - Or load directly from cloud in your agent code

8. Monitor Costs:
    - Check Billing → Overview regularly
    - Set up budget alerts (recommended)

Remember:

- Train locally first, use cloud GPUs only for serious training
- Monitor your credit usage
- Clean up old models and data you don't need

### Possible workflows

#### Training on GPU
1. Train locally on smaller scale
2. When ready, open Vertex AI Notebooks
3. Choose PyTorch + GPU
4. Load data from team storage
5. Train model (uses GPU automatically)
6. Save checkpoints to team storage

#### Sharing Models with Team
1. Save to shared folder in bucket
2. Notify team (model ready)
3. Team members download latest model

#### Generating Training Data
1. Generate data locally for testing
2. When ready, use cloud for large-scale generation
3. Save to team's storage bucket

## Details & Background

### Group setup

#### Individual Students
1. Activate your teaching credits
    - Follow this [link](https://gcp.secure.force.com/GCPEDU?cid=f3NDntEG0aXaI94qaa%2BNXOJURVxoWpbVPM1d2R649n0A19hRTKnkO058YVxHDql2/)
    - Enter name and TU email address
    - Check for confirmation email with coupon code
    - Visit [Google Cloud Console](https://console.cloud.google.com)
    - Use your coupon code
2. Install Google Cloud SDK
3. Monitor your usage:
   - Go to Billing → Overview
   - View remaining credits and usage breakdown
   - Set up billing alerts (recommended)

#### Groups

1. Designate one team member as the project owner
2. Project owner
    - Creates project

        ```bash
        gcloud projects create [PROJECT_ID]
        ```
    - Adds teammates

        ```bash
        gcloud projects add-iam-policy-binding [PROJECT_ID] \
            --member="user:[TEAMMATE_EMAIL]" \
            --role="roles/editor"
        ```

3. Team members:
    - Accept the project invitation via email
    - Use the shared project ID in all commands
    - Monitor shared resource usage in Billing dashboard

#### Setting Up Budget Alerts
```bash
# Create a budget alert at 50% usage
gcloud billing budgets create \
    --billing-account=[BILLING_ACCOUNT_ID] \
    --display-name="Training Budget" \
    --budget-amount=50 \
    --threshold-rules=percent=50 \
    --threshold-rules=percent=90
```

### Storage setup

#### Key points

- You can store whatever you need in a *bucket*
- Buckets can be organized like a directory (folders, files)
- Buckets belong to projects
- Use any logical folder structure that makes sense for your team
- The bucket name is the only part that needs to be unique in your project
- Keep the path structure simple and meaningful

A useful structure could look like this:
```bash
    my-bucket/
    ├── models/
    │   ├── checkpoints/
    │   └── final/
    ├── data/
    │   ├── training/
    │   └── validation/
    └── results/
```

#### Authentication

Before you can use Cloud Storage, you need to:

Select your project:
```bash
gcloud config set project your-project-id
```

And authenticate:
```
# If working locally
gcloud auth application-default login

# If using Google Colab
from google.colab import auth
auth.authenticate_user()
```


#### Creating/Using Storage

After authentication, you can create and use storage buckets:

- I recommend one bucket for your project, and then a folder structure to organize your files
- Bucket names must be globally unique across all of Google Cloud
- Names must be lowercase, no spaces
- A good naming pattern: [project]-[purpose] (e.g., myproject-models)

##### Storage Regions

When creating buckets, it's important to specify a region.

If you don't specify a region:
- The bucket will be created in `us` multi-region
- This can be more expensive
- May have higher latency
- Harder to change later

Choose a region close to where you'll be running your code for best performance.

Common regions:
- `us-central1` - Iowa (good default for US)
- `europe-west1` - Belgium
- `europe-west3` - Frankfurt (best for Germany)
- `asia-east1` - Taiwan


##### Command line

```bash
# Create a bucket
gsutil mb -l europe-west3 gs://my-bucket-name

# Verify the bucket exists
gsutil ls

# Create a folder
gsutil cp /dev/null gs://my-bucket-name/my-folder/.keep
```
Note that `/dev/null` and `.keep` are an empty placeholder directory and file, respectively.

You can verify the folder exists with:
```bash
gsutil ls gs://my-bucket-name/my-folder/
```

Interacting with files/folders inside the bucket works like so:

```bash
# Upload files
gsutil cp local_model.pt gs://my-bucket-name/my-folder/
gsutil cp -r local_folder gs://my-bucket-name/my-folder/  # Upload folder

# Download files
gsutil cp gs://my-bucket-name/my-folder/model.pt ./
gsutil cp -r gs://my-bucket-name/my-folder/models ./  # Download folder

# List contents
gsutil ls gs://my-bucket-name/          # List bucket contents
gsutil ls gs://my-bucket-name/my-folder/**  # List recursively

# Remove files
gsutil rm gs://my-bucket-name/my-folder/old_model.pt
gsutil rm -r gs://my-bucket-name/my-folder/old_folder  # Remove folder
```

The syntax is similar to regular cp/ls/rm commands, but with gs:// paths for bucket locations.

##### Python

Create bucket:
```python
from google.cloud import storage

# Create storage client
client = storage.Client()

# Create a bucket (only need to do this once)
bucket = client.create_bucket('my-bucket-name', location='europe-west3')

# You can verify the bucket was created
for bucket in client.list_buckets():
    print(bucket.name)
```

Once bucket is created:
```python
from google.cloud import storage

# Create client and get bucket
client = storage.Client()
bucket = client.bucket('my-bucket-name')

# Create an empty file to mark the folder
blob = bucket.blob('my-folder/.keep')
blob.upload_from_string('')

# Save model to bucket
with tempfile.NamedTemporaryFile() as tmp:
    # Save model to temp file
    torch.save(model.state_dict(), tmp.name)
    # Upload to bucket
    bucket.blob('my-folder/model.pt').upload_from_filename(tmp.name)
    
# Load model from bucket
with tempfile.NamedTemporaryFile() as tmp:
    # Download to temp file
    bucket.blob('my-folder/model.pt').download_to_filename(tmp.name)
    # Load model
    model.load_state_dict(torch.load(tmp.name)
    
```
You first need to save files locally, before uploading them to the bucket. In principle, you could do this anywhere, or in a `/tmp` folder (if your OS has one). 
Using the `tempfile` module has several advantages:
- Works consistently across platforms (local, Colab, etc.)
- Handles unique filename generation
- Manages proper permissions
- Thread-safe
- Automatic cleanup (can be disabled)


## Working with Vertex AI notebooks

### Creating a notebook

1. In the Google Cloud Console, go to Vertex AI → Workbench → Notebooks
2. Click "NEW NOTEBOOK"
3. Choose:
    - PyTorch environment
    - GPU configuration (e.g., NVIDIA T4 - check pricing beforehand!)
        - **Important:** T4 GPU instances cost ~$0.70/hour. Always:
            - Stop notebooks when not in use
            - Monitor your billing
            - Use GPU only when needed
    - Select your region
4. Click "CREATE"
5. See below for example notebook

### Package management

Best Practices:

1. Keep track of package versions
2. Use requirements.txt for reproducibility
3. Restart kernel after installing new packages
4. (Optional) Consider using environment.yml for conda

Specifically, you can:

- Use pip directly in notebook cells
    ```python
    # In a notebook cell
    !pip install torch numpy pandas
    ```

- Create/update requirements.txt:
    ```bash
    %%writefile requirements.txt
    torch>=2.0.0
    numpy>=1.21.0
    pandas>=1.3.0
    ```
    Or using `pip freeze` or the [tool](https://github.com/bndr/pipreqs) `pipreqs`.

    Then install from requirements:
    ```
    !pip install -r requirements.txt
    ```

- Install custom packages from your project:
    ```python
    # If you have local package code
    !pip install -e /path/to/your/package
    ```

- Create a custom environment:
    ```python
    # Create new conda environment
    !conda create -n connect4 python=3.9

    # Activate environment
    import sys
    !conda activate connect4

    # Install packages
    !pip install torch numpy pandas
    ```

### Use locally developed code in Google Cloud

1. Package your code, i.e., structure it roughly like this:
    ```bash
    your_agent/
    ├── __init__.py
    ├── model.py          # Neural network architecture
    ├── training.py       # Training code
    └── generate_move.py  # Agent interface
    ```

2. Create a requirements file
    ```bash
    # requirements.txt
    torch>=2.0.0
    numpy>=1.21.0
    google-cloud-storage  # For cloud storage access
    ```

3. Use package in Vertex AI notebook
    ```python
    # Install requirements
    !pip install -r requirements.txt

    # Install package
    !pip install -e /path/to/your_agent

    # Now you can import your code
    from their_agent.model import MyNetwork
    from their_agent.training import train_model
    ```



### Example notebook

For completeness, here's a skeleton of an example notebook you could run in a Vertex AI Notebook. It's probably a good idea to reuse code you developed locally (which is under version control and shared between the team members) as much as possible though (see previous section).
```python
    `# training.ipynb

    # Import libraries
    import os
    import tempfile
    import torch
    import numpy as np
    from google.cloud import storage

    # Your Connect4 neural network
    class Connect4Net(nn.Module):
        def __init__(self):
            super().__init__()
            # ... network architecture ...

    # Load or generate training data
    def load_games():
        # Either load from storage:
        client = storage.Client()
        bucket = client.bucket('my-bucket')
        with tempfile.NamedTemporaryFile() as tmp:
            # Download data
            blob = bucket.blob('my-bucket/training_data.npz')
            blob.download_to_filename(tmp.name)
            # Load data
            data = np.load(tmp.name)
            
        # Or generate new games here
        return states, moves

    # Training loop
    def train_model():
        model = Connect4Net().cuda()  # GPU automatically available
        optimizer = torch.optim.Adam(model.parameters())
        
        states, moves = load_games()
        
        for epoch in range(100):
            # ... training code ...
            
            # Save checkpoints directly to Cloud Storage
            if epoch % 10 == 0:
                bucket = storage.Client().bucket('my-bucket')
                with tempfile.NamedTemporaryFile() as tmp:
                    # Save checkpoint
                    torch.save(model.state_dict(), tmp.name)
                    # Upload to storage
                    blob = bucket.blob(f'my-bucket/checkpoint_{epoch}.pt')
                    blob.upload_from_filename(tmp.name)

    # Run training
    train_model()`
```

### Beyond Vertex AI Notebooks

It's possible that you run into issues in case you need to scale up the approach using Vertex AI Notebooks. It's relatively time-consuming to start, monitor and stop a notebook, and this can become tedious very quickly.

Two possible options are:

1. Vertex AI Training Jobs
    - More scalable than notebooks
    - Can run multiple jobs in parallel
    - Proper job management and monitoring
    - Automatic resource cleanup
2. Google Kubernetes Engine (GKE)
    - Most flexible
    - Can run many training pods
    - Good for distributed training
    - More complex to set up

I recommend using Vertex AI Training jobs due to the smaller overhead. But Kubernetes is worth looking into. Deploying things on a large scale is a sought-after skill!

In order to use Vertex AI Training jobs, you have to containerize your code. I'll talk about that more next week. In very simple terms this means that you bundle up your code and the necessary dependencies and create a set of instructions on how to set it up. The magic of containerization is that these instructions are used to create something close to a separate system running on your hardware, which should be enough to use your code on any machine. This means you have to take care of all the setup only once and then just worry about what to run where.

When using Docker for containerization, the 'instructions' are in a *Dockerfile*. This is basically a text file with commands that install things (you can think of installing stuff on a machine without an operating system). 

The Dockerfile is used to build an *image*, which is a blueprint for the actual running system we want. Think of it as a system with everything set up, waiting for you to actually run it. Conveniently, images can be built up in layers and you can use external images as a starting point for your custom image. You can basically use a template that sets up a linux machine with only Python installed, and go from there.

Now you can use this image and 'run' it by creating a *container*. In the previous analogy, the container would be the running system.

In short, to use Vertex AI Notebooks, you have to follow these steps:

1. Package your code as described above
2. Create Dockerfiles for what you want to run
3. Build the images you need
4. Push the images
5. Submit jobs running containers
