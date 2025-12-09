---
title: "Multi-Region keys: A new approach to key replication in AWS Payment Cryptography"
date: 2025-09-16
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

Published: 2025‑09‑16 – Authors: Ruy Cavalcanti and Mark Cline in [Announcements](https://aws.amazon.com/blogs/security/category/post-types/announcements/), [Financial Services](https://aws.amazon.com/blogs/security/category/industries/financial-services/), [Industries](https://aws.amazon.com/blogs/security/category/industries/), [Intermediate (200)](https://aws.amazon.com/blogs/security/category/learning-levels/intermediate-200/), [Security, Identity, & Compliance](https://aws.amazon.com/blogs/security/category/security-identity-compliance/), [Technical How-to](https://aws.amazon.com/blogs/security/category/post-types/technical-how-to/)

In our previous post (Part 1 of the key replication series), [Automatically replicate your card payment keys across AWS Regions](https://aws.amazon.com/blogs/security/automatically-replicate-your-card-payment-keys-across-aws-regions/), we explored an event-driven serverless architecture using [AWS PrivateLink](https://aws.amazon.com/privatelink) to securely replicate card payment keys between AWS Regions. That solution illustrated how to build a custom payment cryptography key replication framework.

Based on customer feedback requesting a more automated, code-free approach, we are excited to announce an additional option with **Multi-Region keys** for **AWS Payment Cryptography** in Part 2 of the series.

With this new feature, you can automatically synchronize payment cryptography keys from a primary Region to other Regions you choose, improving fault tolerance and availability of payment applications. You can also choose between account-level or key-level replication, providing more flexibility in managing payment keys across Regions.

---

## **Multi-Region keys: Overview and benefits**

The new **Multi-Region** key replication feature for [AWS Payment Cryptography](https://aws.amazon.com/payment-cryptography/) provides you with flexible control over key replication strategy through the following key capabilities:

* Decide whether keys are replicated  
* Choose specific Regions to replicate keys to  
* Manage replication configuration changes  
* Configure replication at the account or key level based on business needs

**Multi-Region keys** provide many benefits for global payment operations, including:

* **Increased availability**: Access payment keys even when one Region is unavailable.  
* **Disaster recovery capability**: Maintain business operations during incidents by replicating keys between Regions.  
* **Global operations**: Support payment processing across multiple geographic regions.  
* **Simplified management**: Centralized control with distributed capabilities.  
* **Consistent key IDs**: Same key IDs across Regions simplify application development.

---

## **Configuration options**

**Payment Cryptography** provides two distinct methods to configure Multi-Region key replication, giving you flexibility in implementing the strategy that best fits your organization: you can choose between a broad approach (account-level) or a more granular approach (key-level).

### **Account-level**

With account-level configuration, AWS automatically copies exportable symmetric keys created in your Payment Cryptography account from the primary Region you specify to other Regions you specify. This simplifies key management in multi-Region deployments, provides consistent key availability in your chosen Regions, and reduces operational costs for key management.

To configure account-level replication using the [AWS Command Line Interface (AWS CLI)](https://aws.amazon.com/cli), use the new API enable-default-key-replication-regions to set the Regions where AWS will replicate your keys. To remove a Region from the default replication list, use the API disable-default-key-replication-regions.

Note: Only symmetric keys created after account-level replication is enabled will be replicated.

## **Key-level replication**

By using **key-level replication**, you can achieve more granular control by:

* Designating specific keys as **multi-Region keys**.  
* Defining **custom replication targets** for each multi-Region key.  
* Maintaining separate keys for each Region when needed.

**Note:** Within each Region, Payment Cryptography maintains key redundancy across multiple Availability Zones to ensure high availability. **Multi-Region key replication** extends this geographically, enhancing resilience to inter-Region connectivity failures while maintaining control over key storage location.

You can specify replication Regions when creating keys using the \--replication-regions parameter in **AWS CLI**, through the create-key or import-key APIs. For existing keys, you can use the new APIs add-key-replication-regions and remove-key-replication-regions to manage which Regions will receive key replicas.

**Important:** When you specify replication Regions during key creation, these settings take precedence over the default account-level replication configuration.

---

## **How it works**

Figure 1 describes the process when you replicate a key in **Payment Cryptography**:

1. The key is created in the **primary Region** you specify.  
2. **Payment Cryptography** automatically **asynchronously replicates** the **key material** to the Regions selected as replicas.  
3. Replicated keys maintain the **same Key ID** across all Regions — only the **Region** portion of the **Amazon Resource Name (ARN)** changes.  
4. The key in the primary Region is labeled MultiRegionKeyType: PRIMARY  
5. Keys in replica Regions are labeled MultiRegionKeyType: REPLICA and have a reference to the primary Region.  
6. When deleting a key, the deletion **automatically cascades** from the primary key to replicas in other Regions.


| ![](/images/3-BlogsTranslated/Blog3/img1.png)

Figure 1: Illustration of key replication process from us-east-1 to us-west-2

### **Example: Creating a multi-Region key at key level**

The following example illustrates creating a **card verification key (CVK)** in the **primary Region (us-east-1)** with replication to **us-west-2**:

aws payment-cryptography create-key \\

\--exportable \\

\--key-attributes KeyAlgorithm=TDES\_2KEY,\\

KeyUsage=TR31\_C0\_CARD\_VERIFICATION\_KEY,\\

KeyClass=SYMMETRIC\_KEY,KeyModesOfUse='{Generate=true,Verify=true}' \\

\--region us-east-1 \\

\--replication-regions us-west-2

The response shows the key is being created and replication is in progress:

{
  "Key": {
    "KeyArn": "arn:aws:payment-cryptography:us-east-1:111122223333:key/qs6643jl4ohibtqk",
    "KeyAttributes": {
      "KeyUsage": "TR31\_C0\_CARD\_VERIFICATION\_KEY",
      "KeyClass": "SYMMETRIC\_KEY",
      "KeyAlgorithm": "TDES\_2KEY",
      "KeyModesOfUse": {
        "Encrypt": false,
        "Decrypt": false,
        "Wrap": false,
        "Unwrap": false,
        "Generate": true,
        "Sign": false,
        "Verify": true,
        "DeriveKey": false,
        "NoRestrictions": false
      }
    },
    "KeyCheckValue": "CC5EE2",
    "KeyCheckValueAlgorithm": "ANSI\_X9\_24",
    "Enabled": true,
    "Exportable": true,
    "KeyState": "CREATE\_COMPLETE",
    "KeyOrigin": "AWS\_PAYMENT\_CRYPTOGRAPHY",
    "CreateTimestamp": "2025-08-21T15:25:54.475000-03:00",
    "UsageStartTimestamp": "2025-08-21T15:25:54.287000-03:00",
    "MultiRegionKeyType": "PRIMARY",
    **"ReplicationStatus": {**
      **"us-west-2": {**
        **"Status": "IN\_PROGRESS"**
      **}**
    },
    "UsingDefaultReplicationRegions": false
  }
}

When replication completes, the status will be updated to **SYNCHRONIZED**:

aws payment-cryptography get-key \\

\--key-identifier arn:aws:payment-cryptography:us-east-1:111122223333:key/qs6643jl4ohibtqk \\

\--region us-east-1

The response shows the primary key has completed synchronization:

"ReplicationStatus": {
      "us-west-2": {
        "Status": "SYNCHRONIZED"
      }
    }

You can access the key in the **replica Region (us-west-2)** using the same **Key ID**, only changing the Region name:

aws payment-cryptography get-key \\

\--key-identifier arn:aws:payment-cryptography:us-west-2:111122223333:key/qs6643jl4ohibtqk \\

\--region us-west-2

The response displays the replica key with a reference to the primary Region (**PrimaryRegion: us-east-1**).

---

## **Important considerations**

When using **multi-Region keys**, there are several important points to consider:

* **Only exportable symmetric keys are supported**, asymmetric keys are not supported.  
* **Costs are calculated separately for each Region** — for example, replicating to 3 Regions will incur costs for the primary key plus costs for each replica key.  
* **Key aliases** and **tags** need to be managed separately in each Region as they **are not automatically replicated**.  
* **Primary keys** can be edited or updated, while **replica keys** are **read-only** and support cryptographic operations. All changes must be made to the primary key and **Payment Cryptography** will automatically synchronize to replica Regions.  
* **Monitor replication status** to ensure changes have been successfully synchronized.

### **Key deletion behavior**

When scheduling deletion of a primary key:

* All replica keys will be deleted **immediately**.  
* The primary key will transition to **pending deletion** status for a minimum of 3 days, during which time deletion can be **canceled**.  
* If you restore the primary key, you need to re-enable replication to recreate replicas in desired Regions.  
* After the 3-day period passes, the primary key will be **permanently deleted** and cannot be recovered.  
* Deleting a **replica key** only affects that Region, **does not affect** the primary key or other replicas.

### **Eventual consistency model**

Multi-Region key replication operates on an **eventual consistency** model — meaning changes may not appear immediately across all Regions.  
 Therefore, **applications should be designed to handle this model** and not assume that keys or changes will be immediately available in replica Regions.

If your application requires **strong consistency**, implement a **polling** mechanism using the [GetKey](https://docs.aws.amazon.com/payment-cryptography/latest/APIReference/API_GetKey.html) API to verify that changes have been synchronized before proceeding with cryptographic operations.

---

## **Logging and monitoring**

**Payment Cryptography** records API activity through [AWS CloudTrail](https://aws.amazon.com/cloudtrail), which now includes new events and attributes specifically for the Multi-Region key replication feature.

### **New CloudTrail events**

The service records a new event type called SynchronizeMultiRegionKey, which appears in both the primary and replica Regions.

#### **Events in the primary Region:**

Each defined replica Region generates two **SynchronizeMultiRegionKey** events in the primary Region:

1. An event related to the key export process.  
   "serviceEventDetails": {  
           "keyArn": "arn:aws:payment-cryptography:us-east-1:111122223333:key/qs6643jl4ohibtqk",  
           "replicationRegion": "us-west-2",  
           **"replicationType": "ExportKeyReplica"**  
       },

2. An event related to the key import process.  
   "serviceEventDetails": {  
           "keyArn": "arn:aws:payment-cryptography:us-east-1:111122223333:key/qs6643jl4ohibtqk",  
           "replicationRegion": "us-west-2",  
           **"replicationType": "ImportKeyReplica"**  
       },

#### **Events in replica Regions:**

Each replica Region records a **SynchronizeMultiRegionKey** event corresponding to the key import process:

"eventName": "SynchronizeMultiRegionKey",

"awsRegion": "us-west-2",

"serviceEventDetails": {

        "keyArn": "arn:aws:payment-cryptography:us-west-2:111122223333:key/qs6643jl4ohibtqk",

        "replicationRegion": "us-west-2",

        **"replicationType": "ImportKeyReplica"**

    },

---

### **New CloudTrail attributes**

**Key management APIs** now include **new attributes** to reflect Multi-Region replication activity.

For example, the [CreateKey](https://docs.aws.amazon.com/payment-cryptography/latest/APIReference/API_CreateKey.html) event in the **primary Region (us-east-1)** now includes the attribute:

"requestParameters": {
    **"replicationRegions": \["us-west-2"\]**
 }

The **CreateKey** event in the **replica Region (us-west-2)** will reflect the corresponding replica key initialization process, with similar information about KeyUsage, Algorithm, and KeyCheckValue.

---

## **Getting started**

To get started with **Multi-Region key replication** in **Payment Cryptography**, follow these steps:

1. Identify the **primary Region**.

2. Identify **replica Regions** and decide whether you will use **account-level** or **key-level** configuration.

3. Create new **exportable symmetric keys** or update existing keys to enable **Multi-Region replication**.

4. Update your application to use **consistent Key IDs** across Regions.

---

## **Conclusion**

The new **Multi-Region key replication** feature in **AWS Payment Cryptography** enhances automatic key replication capabilities, improving **fault tolerance**, **availability**, and **simplifying global payment key management**. This feature ensures that your **payment cryptography keys** are always available **anytime, anywhere**, while providing flexibility in choosing replication strategies between **account-level** and **key-level**, helping organizations optimize performance, security, and costs in multi-Region environments.

| ![](/images/3-BlogsTranslated/Blog3/author1.jpg) | Ruy Cavalcanti Ruy is a Senior Security Architect specializing in Latin American financial services at AWS. He has worked in IT and Security for over 19 years, helping customers build security architectures and solve data protection and compliance challenges. When not designing security solutions, he enjoys playing guitar, cooking Brazilian barbecue, and spending time with family and friends. |
| :---- | :---- |
| ![](/images/3-BlogsTranslated/Blog3/author2.jpg)| **Mark Cline** Mark is Head of Product Management at AWS Payments, where he has over 15 years of experience in financial services across multiple use cases and industries. He collaborates with leading banks, financial institutions, and technology providers to reduce the burden on payment systems, enabling customers to focus on innovation. When not busy simplifying payments, you can find him coaching little league baseball teams or running. |
