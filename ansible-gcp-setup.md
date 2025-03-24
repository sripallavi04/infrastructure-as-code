Prerequisites
Ansible Installed: Ensure Ansible is installed on your local machine.
GCP Account: Have a Google Cloud Platform (GCP) account with necessary permissions to create Compute Engine instances.
GCP SDK Installed and Configured: Install and configure the Google Cloud SDK with your GCP credentials.
Service Account Key: Create and download a service account key with the required permissions to manage Compute Engine instances.
Step-by-Step Instructions
1. Set Up GCP Project and Service Account
Log in to GCP Console:

Navigate to the GCP Console (https://console.cloud.google.com).
Create a New Project (if not already created):

Go to the project selector and create a new project.
Enable Compute Engine API:

In the API & Services section, ensure the Compute Engine API is enabled for your project.
Create a Service Account:

Navigate to IAM & Admin > Service accounts.
Create a new service account with the necessary roles (e.g., Compute Admin).
Generate a JSON key for this service account and download it to your local machine.
2. Install Required Ansible Collections and Roles
Ensure Python and pip are Installed:

Make sure Python and pip are installed on your machine.
Install Ansible Collection for GCP:

Use Ansible Galaxy to install the required GCP collection.



ansible-galaxy collection install google.cloud
3. Create the Ansible Playbook
Create a Project Directory:

Open your terminal or command prompt.
Create a new directory for your project and navigate to it.


mkdir ansible-gcp-project
cd ansible-gcp-project
Create the Playbook File:

Inside your project directory, create a new file named gcp_instance.yml.



touch gcp_instance.yml
Define the Ansible Playbook:

Edit the gcp_instance.yml file to include the tasks for provisioning a GCP Compute Engine instance.


---
- name: Provision GCP Compute Engine Instance
  hosts: localhost
  gather_facts: False
  collections:
    - google.cloud

  vars:
    service_account_file: "/path/to/your/service-account-file.json"
    project_id: "your-gcp-project-id"
    zone: "us-central1-a"
    machine_type: "n1-standard-1"
    instance_name: "my-ansible-instance"
    image_family: "debian-9"
    image_project: "debian-cloud"

  tasks:
    - name: Create a GCP Compute Engine instance
      google.cloud.gce_instance:
        name: "{{ instance_name }}"
        machine_type: "{{ machine_type }}"
        zone: "{{ zone }}"
        project_id: "{{ project_id }}"
        auth_kind: serviceaccount
        service_account_file: "{{ service_account_file }}"
        disks:
          - auto_delete: true
            boot: true
            type: pd-standard
            initialize_params:
              source_image: "projects/{{ image_project }}/global/images/family/{{ image_family }}"
        network_interfaces:
          - network: default
            access_configs:
              - name: External NAT
                type: ONE_TO_ONE_NAT
      register: instance

    - name: Display the instance information
      debug:
        msg: "Instance {{ instance_name }} is created with IP {{ instance.instance_data.networkInterfaces[0].accessConfigs[0].natIP }}"
4. Update Variables with Actual Values
Replace /path/to/your/service-account-file.json with the path to your service account key file.
Replace your-gcp-project-id with your actual GCP project ID.
Adjust other variables (zone, machine_type, instance_name, image_family, image_project) as needed.
5. Run the Ansible Playbook
Run the Playbook using the ansible-playbook command.


ansible-playbook gcp_instance.yml
6. Verify the Setup
Check the GCP Console:

Navigate to the GCP Console and verify that the Compute Engine instance has been created in the specified project and zone.
Check the Playbook Output:

The playbook output should display information about the created instance, including its external IP address.
7. Cleanup
Delete the Compute Engine Instance (if needed):
Use the GCP Console or create an Ansible task to delete the instance when it is no longer needed to avoid unnecessary costs.



- name: Delete the GCP Compute Engine instance
  google.cloud.gce_instance:
    name: "{{ instance_name }}"
    zone: "{{ zone }}"
    project_id: "{{ project_id }}"
    auth_kind: serviceaccount
    service_account_file: "{{ service_account_file }}"
    state: absent
By following these steps, you can create a simple Ansible playbook to provision a Google Compute Engine instance. This approach ensures a consistent, repeatable, and automated process for managing your GCP infrastructure.



