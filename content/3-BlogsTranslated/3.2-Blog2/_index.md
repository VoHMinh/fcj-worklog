---
title: "Amazon SageMaker introduces Amazon S3 based shared storage for enhanced project collaboration"
date: 2025-09-16
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

Published: 2025‑09‑16 – Authors: Hari Ramesh, Anagha Barve, Anchit Gupta, Saurabh Bhutyani and Zach Mitchell in [Amazon SageMaker Unified Studio](https://aws.amazon.com/blogs/big-data/category/analytics/amazon-sagemaker-unified-studio/), [Amazon Simple Storage Service (S3)](https://aws.amazon.com/blogs/big-data/category/storage/amazon-simple-storage-services-s3/), [Announcements](https://aws.amazon.com/blogs/big-data/category/post-types/announcements/), [Intermediate (200)](https://aws.amazon.com/blogs/big-data/category/learning-levels/intermediate-200/),[Technical How-to](https://aws.amazon.com/blogs/big-data/category/post-types/technical-how-to/).

AWS recently announced that [Amazon SageMaker](https://aws.amazon.com/sagemaker/) now provides [Amazon Simple Storage Service](https://aws.amazon.com/s3) (Amazon S3) as the default file storage option for new projects in [Amazon SageMaker Unified Studio](https://aws.amazon.com/sagemaker/unified-studio/). This feature addresses the deprecation of [AWS CodeCommit](https://aws.amazon.com/codecommit/), while providing teams with a simple and consistent way to collaborate on project files across all of SageMaker's integrated development tools.

This new Amazon S3 storage option provides the following benefits:

* **Simple collaboration** – Share files directly between project members without Git operations.  
* **Consistent access** – Files are accessed consistently across SageMaker tools (JupyterLab, Query Editor, Visual ETL).  
* **Clear workspace separation** – Personal storage separation is available with [Amazon Elastic Block Store](https://aws.amazon.com/ebs) (Amazon EBS) volumes.  
* **Global availability** – Supported in all AWS Regions where SageMaker is available.

Although Amazon S3 is the default option for file storage, you can still use Git version control if you want more robust source code management capabilities.

In this article, we will discuss the new feature and how to get started using **Amazon S3 shared storage** in **SageMaker Unified Studio**.

---

**Solution Overview**

When you create a new **SageMaker Unified Studio domain**, the service automatically configures **Amazon S3** as the default storage option for projects. Each project receives a dedicated shared storage area in Amazon S3, available to project members, with the structure:

\[bucket\]/\[domain-id\]/\[project-id\]/shared/.

### SageMaker tools (JupyterLab and Code Editor) provide users with:

* A **personal EBS volume** for private work in JupyterLab and Code Editor.  
* A mounted shared folder containing the project's shared storage space on Amazon S3.  
* Clear separation between personal and shared spaces.

### Shared storage access capabilities in SageMaker integrated development tools:

* **JupyterLab** and **Code Editor** display shared files alongside personal files.  
* **Query Editor** filters relevant SQL notebooks.  
* **Visual ETL** provides direct access to shared ETL (extract, transform, load) workflows.

Files saved to the shared folder are immediately available and visible to project members. Users can continue working with personal files in **EBS volumes** (for example in JupyterLab and Code Editor) and can move files to shared storage when ready to collaborate. If you want to use Git for collaboration, you can still do so by integrating the project with GitHub, GitLab, or repositories managed on Bitbucket.

---

## **Migration options and version control**

For teams currently using **Amazon CodeCommit**, existing projects will continue to work normally. New projects will default to **Amazon S3** storage. If you want **version control** functionality for Amazon S3-based projects, you can enable **versioning directly** in Amazon S3.

---

## **Prerequisites**

Before following the instructions in the next section, you need to complete the following prerequisites:

1. [Sign up for an AWS account](https://docs.aws.amazon.com/sagemaker-unified-studio/latest/adminguide/setting-up.html#sign-up-for-aws)**.**  
2. [Create a user with administrative access](https://docs.aws.amazon.com/sagemaker-unified-studio/latest/adminguide/setting-up.html#create-an-admin)**.**  
3. [Enable IAM Identity Center](https://docs.aws.amazon.com/singlesignon/latest/userguide/enable-identity-center.html) in the same **AWS Region** where you want to create a **SageMaker Unified Studio domain**. Confirm which Regions SageMaker Unified Studio is currently available in. Set up an **Identity Provider (IdP)** and synchronize identities and groups with **IAM Identity Center**. For more details, see [IAM Identity Center Identity source tutorials](https://docs.aws.amazon.com/singlesignon/latest/userguide/tutorials.html).

---

## **Getting started with Amazon S3 Shared Storage**

To get started with **Amazon S3 shared storage**, complete the following steps:

1. Create a new **SageMaker Unified Studio domain**.  

![](/images/3-BlogsTranslated/Blog2/img1.png)

2. Create a new project (Amazon S3 storage will be selected by default as the file storage option).  
![](/images/3-BlogsTranslated/Blog2/img2.png)

3. Open the new project and select **JupyterLab** from the **Build** menu.  
![](/images/3-BlogsTranslated/Blog2/img3.png)

4. Save the new notebook you just created.  
![](/images/3-BlogsTranslated/Blog2/img4.png)

5. Rename the file as desired.  
   ![](/images/3-BlogsTranslated/Blog2/img5.png)


After the project is saved, users in the project can view the saved notebook in the **Project files** section at the S3 path:

\[bucket\]/\[domain-id\]/\[project-id\]/shared/.

---
![](/images/3-BlogsTranslated/Blog2/img6.png)

### **Enable version control with Git**

To enable **version control** with Git, follow these steps:

1. Access the **SageMaker console** and create a new **project profile**.  
![](/images/3-BlogsTranslated/Blog2/img7.png)

2. Provide all necessary information for your project profile.  
![](/images/3-BlogsTranslated/Blog2/img8.png)

3. In the **Project files storage** section, the **Amazon S3** option is selected by default. If you want to enable version control for the project, you can use existing Git repository connections by selecting **Git repository**.
![](/images/3-BlogsTranslated/Blog2/img9.png)


---

### **Using shared storage in Query Editor**

To use the **shared storage** feature in **Query Editor**, follow these steps:

1. Select **Query Editor** from the **Build** menu.  
![](/images/3-BlogsTranslated/Blog2/img10.png)

2. Compose your query, then in the **Actions** menu, select **Save** to save the query to shared storage.  
![](/images/3-BlogsTranslated/Blog2/img11.png)

3. Return to the **Project files** section, where you can view query notebook files at the S3 path:  
   \[bucket\]/\[domain-id\]/\[project-id\]/shared/.
![](/images/3-BlogsTranslated/Blog2/img12.png)

---

## **Using shared storage in Visual ETL flows**

To use the **shared storage** feature in **Visual ETL flows**, follow these steps:

1. Select **Visual ETL flows** from the **Build** menu.  
![](/images/3-BlogsTranslated/Blog2/img13.png)

2. Develop your ETL workflow and save the code to the project. 
![](/images/3-BlogsTranslated/Blog2/img14.png)

3. Return to the **Project files** section, where you can view files at the S3 path: \[bucket\]/\[domain-id\]/\[project-id\]/shared/jobs/uploads/\<ETL name\>.
![](/images/3-BlogsTranslated/Blog2/img15.png)

---

## **Cleanup**

Make sure you delete SageMaker Unified Studio resources after completion to avoid unexpected charges. The process includes the following steps:

1. **Delete projects.**  
2. **Delete domain.**  
3. **Delete S3 bucket** with the name format:  
   amazon-datazone-AWSACCOUNTID-AWSREGION-DOMAINID

---

## **Conclusion**

The launch of Amazon S3 shared storage in SageMaker is another step in simplifying the analytics and machine learning (ML) development experience for customers. By reducing the complexity of Git operations while maintaining strong collaboration capabilities, teams can focus more on building and deploying analytics and ML solutions faster. This feature is now available in AWS Regions where SageMaker is supported.

For detailed information about this feature, including setup instructions and best practices, please refer to the [Unified storage in Amazon SageMaker Unified Studio](https://docs.aws.amazon.com/sagemaker-unified-studio/latest/userguide/storage.html) documentation.

---

| ![](/images/3-BlogsTranslated/Blog2/author1.jpeg) | Hari Ramesh [Hari](https://www.linkedin.com/in/haryramesh/) is a Senior Analytics Specialist Solutions Architect at AWS. He focuses on building cloud data platforms that enable real-time online, big data processing, and robust data governance. |
| :---- | :---- |
| ![](/images/3-BlogsTranslated/Blog2/author2.jpg) | **Anagha Barve** [Anagha](https://www.linkedin.com/in/anagha-barve-9a86b8/) is a Software Development Manager in the Amazon SageMaker Unified Studio team. Her team focuses on building integrated tools and experiences for developers using Amazon SageMaker Unified Studio. In her spare time, she enjoys cooking, gardening, and traveling. |
| 
| ![](/images/3-BlogsTranslated/Blog2/author3.jpg) | **Zach Mitchell** [Zach](https://www.linkedin.com/in/zachary-mitchell-ab882853/) is a Sr. Big Data Architect. He works in the product team to enhance understanding between product engineers and their customers while guiding customers throughout their data lake and other data solution development journey on AWS analytics services. |
| 
| ![](/images/3-BlogsTranslated/Blog2/author4.jpg) | **Saurabh Bhutyani** [Saurabh](https://www.linkedin.com/in/s4saurabh/) is a Principal Analytics Specialist Solutions Architect at AWS. He is passionate about new technology. He joined AWS in 2019 and works with customers to provide architectural guidance for operating generative AI use cases, scalable analytics solutions, and data mesh architectures using AWS services such as Amazon Bedrock, Amazon SageMaker, Amazon EMR, Amazon Athena, AWS Glue, AWS Lake Formation, and Amazon DataZone. |
| 
| ![](/images/3-BlogsTranslated/Blog2/author5.jpg) | **Anchit Gupta** [Anchit](https://www.linkedin.com/in/anchitgupta92/) is a Senior Product Manager for Amazon SageMaker Studio. She focuses on facilitating interactive data science and data engineering workflows from within the SageMaker Studio IDE. In her spare time, she enjoys cooking, playing board/card games, and reading. |
