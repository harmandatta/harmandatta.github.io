---
layout: post
title: "Jenkinsfile Approval Stage: Usage, Types, and Implementation"
description: |
  Understand how to implement an approval stage in Jenkins pipelines. This post explores its usage, benefits, and types including manual input, choice, and yes/no approvals.
categories: devops
tags: "devops jenkins"
level: Intermediate
readtime: 10 min
thumbnail: /assets/img/posts/jenkinsfile-approval/thumbnail.jpg
---
## Jenkinsfile Approval Stage
In modern CI/CD pipelines, not every step should be automated—particularly when it comes to production deployments or critical actions. For such cases, Jenkins provides a way to pause pipeline execution and wait for manual approval using the `input` directive.

This feature adds a layer of human validation, making it suitable for quality gates, business approvals, or release management.


## Usage
The approval stage is used when:
- A team lead or QA needs to approve deployment
- Risk mitigation is required before production actions
- Decisions based on environment, business readiness, or external factors need to be made

Jenkins uses the `input` step inside a stage to halt pipeline execution and prompt for manual intervention.

## Implementation
### Basic Yes/No Approval Example
```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }

        stage('Approval') {
            steps {
                input message: 'Do you want to proceed to deployment?', ok: 'Yes'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
            }
        }
    }
}
```

## Types of Approval Inputs
Jenkins supports multiple types of manual approvals using the `input` step:

### 1. Simple Yes/No Approval
```groovy
input message: 'Deploy to Production?', ok: 'Yes'
```

### 2. Choice-Based Approval
```groovy
input message: 'Select environment for deployment:',
      parameters: [
          choice(name: 'Environment', choices: ['staging', 'production'], description: 'Choose environment')
      ]
```

### 3. Text Input
```groovy
input message: 'Enter deployment version:',
      parameters: [
          string(name: 'VERSION', defaultValue: 'v1.0.0', description: 'Version to deploy')
      ]
```

### 4. Input with Timeout
You can use a `timeout` block to automatically fail or proceed if no input is provided within a defined time.
```groovy
stage('Approval') {
    steps {
        timeout(time: 10, unit: 'MINUTES') {
            input message: 'Approve deployment?', ok: 'Proceed'
        }
    }
}
```

This will wait for 10 minutes. If no action is taken, the pipeline will abort.


## Image: Jenkins Pipeline with Approval Stage
Below is a conceptual diagram of a Jenkins pipeline with an approval stage:
```
[Checkout] ---> [Build] ---> [Approval (Manual Input)] ---> [Deploy]
```

![Approval Stage Image]({{ "/assets/img/posts/jenkinsfile-approval/approval-stage.webp" }})

If the approval is granted, the pipeline proceeds. If rejected or timed out, the pipeline fails or halts depending on configuration.

## Benefits
- Human validation for critical stages
- Prevents unintended changes from being deployed
- Enables custom branching and logic
- Provides audit trails for approvals

## Considerations
- Ensure users have proper permissions to approve
- Combine with `timeout` for better control
- Document who approves what and why for audit readiness

## Conclusion
Jenkins approval stages are essential for controlled and secure deployments. They give teams a chance to verify and intervene before high-risk actions. With support for yes/no inputs, options, and text parameters, Jenkins pipelines can flexibly integrate manual steps where necessary.

*Happy Pipelin-ing!*