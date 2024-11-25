# CloudFormation Infrastructure Deployment with CI/CD Pipeline

## **Overview**
This project automates the deployment of AWS infrastructure using CloudFormation templates and a CI/CD pipeline built with AWS CodePipeline. It includes VPC, Route Tables, NAT Gateway, EC2 Instances, and Security Groups, and integrates validation tools like `cfn-lint` and `Taskcat` to ensure the correctness of the CloudFormation templates.

---

## **Environment Setup**

### **Prerequisites**
1. **AWS Account**: Ensure you have access to an AWS account with sufficient permissions.
2. **IAM Roles and Policies**:
   - IAM Role for CodePipeline with permissions:
     - `AWSCodePipelineFullAccess`
     - `AWSCodeBuildAdminAccess`
     - `CloudFormationFullAccess`
   - IAM Role for CodeBuild:
     - `AmazonS3FullAccess`
     - `CloudFormationFullAccess`
  
3. **AWS CLI Installed**: Install the AWS CLI and configure it with your credentials:
   ```bash
   aws configure
   ```
4. **Tools**:
   - Install `cfn-lint`:
     ```bash
     pip install cfn-lint
     ```
   - Install `Taskcat`:
     ```bash
     pip install taskcat
     ```
5. **Git Repository**:
   - Create an AWS CodeCommit repository and clone it locally.

---

## **Project Structure**

```plaintext
project-directory/
|— README.md                  # This documentation file
|— buildspec.yml               # Configuration for CodeBuild
|— template.yaml              # CloudFormation template
|— .taskcat.yml               # Taskcat configuration
```

### **File Details**
- **template.yaml**: Defines the AWS infrastructure resources.
- **buildspec.yml**: Specifies build instructions for CodeBuild, including validation steps.
- **.taskcat.yml**: Configuration file for Taskcat to test the CloudFormation template.

---

## **Steps to Set Up and Run**

### **1. Clone Repository**
Clone your AWS CodeCommit repository:
```bash
git clone <your-codecommit-repo-url>
cd <your-repository>
```

### **2. Add Files to Repository**
Place the following files in the repository:
- `template.yaml`
- `buildspec.yml`
- `.taskcat.yml`

Commit and push changes:
```bash
git add .
git commit -m "Add initial CloudFormation and CI/CD configuration"
git push origin main
```

### **3. Configure AWS CodePipeline**
1. **Source Stage**:
   - Select your AWS CodeCommit repository as the source.
   - Choose the branch where your files are located (e.g., `main`).

2. **Build Stage**:
   - Create a new CodeBuild project.
   - Use the `buildspec.yml` file included in the repository.
   - Configure output artifacts:
     - Name: `BuildArtifact`

3. **Deploy Stage**:
   - Choose AWS CloudFormation as the deployment provider.
   - Configure input artifact:
     - Name: `BuildArtifact`
   - Specify template file path:
     ```
     BuildArtifact::template.yaml
     ```
   - Stack name: `Lab2Stack`
   - Enable IAM capabilities: `CAPABILITY_NAMED_IAM`

Save the pipeline configuration and start the pipeline.

---

## **Validation and Deployment**

### **1. Validation with `cfn-lint`**
During the build stage, `cfn-lint` validates the syntax of the `template.yaml`. Any errors will fail the pipeline.

### **2. Testing with Taskcat**
Taskcat tests the `template.yaml` by deploying it in multiple AWS regions as specified in `.taskcat.yml`. If the tests fail, review the logs in CodeBuild.

### **3. Deployment**
Once the template passes validation and testing, the Deploy stage creates or updates the CloudFormation stack. Verify that the following resources are created:
- **VPC**
- **Public and Private Subnets**
- **NAT Gateway**
- **Internet Gateway**
- **Route Tables**
- **Security Groups**
- **EC2 Instance**

---

## **Checking Results**

### **1. Verify CloudFormation Stack**
Go to **AWS Management Console > CloudFormation**:
- Check the status of the stack `Lab2Stack`.
- View resources created under the **Resources** tab.

### **2. Verify Resources**
1. **VPC**:
   - Navigate to **VPC > Your VPCs**.
   - Look for the VPC named `Lab2_MyVPC`.

2. **Subnets**:
   - Check for public and private subnets under **VPC > Subnets**.

3. **NAT Gateway**:
   - Verify NAT Gateway in **VPC > NAT Gateways**.

4. **EC2 Instance**:
   - Check the instance in **EC2 > Instances**.
   - Ensure it is running and has the correct security group attached.

---

## **Troubleshooting**
- **Pipeline Fails in Build Stage**:
  - Check CodeBuild logs to identify issues with `cfn-lint` or `Taskcat`.

- **Stack Deployment Fails**:
  - Review CloudFormation **Events** tab for specific error messages.
  - Verify IAM roles have the necessary permissions.

- **Resources Missing**:
  - Ensure all dependencies (e.g., Internet Gateway, NAT Gateway) are correctly linked in `template.yaml`.

---

## **Conclusion**
This project automates AWS infrastructure deployment with validation and testing steps integrated into a CI/CD pipeline. Modify and extend the CloudFormation template and pipeline configuration to fit your specific use case.

