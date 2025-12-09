---
title: "Let's Architect! Modern Data Architectures"
date: 2024-11-05
weight: 15
chapter: false
pre: " <b> 3.4. </b> "
---

Post date: Nov 05, 2024 – Authors: Luca Mezzalira, Federica Ciuffo, Vittorio Denti, and Zamira Jaupaj in [Amazon Machine Learning](https://aws.amazon.com/machine-learning/), [Amazon S3](https://aws.amazon.com/s3/), [Architecture](https://aws.amazon.com/architecture/), [Artificial Intelligence](https://aws.amazon.com/ai/), [AWS Big Data](https://aws.amazon.com/big-data/).

---

Data is the fuel for AI; modern data is even more critical for generative AI and advanced data analytics, enabling more accurate, relevant, and impactful results. Modern data comes in many forms: real-time, unstructured, or user-generated. Each form requires a distinct solution.

AWS's data journey began with **Amazon Simple Storage Service (Amazon S3)** in 2006, marking the beginning of cloud storage at scale. Since then, AWS has expanded its data services to cover the entire data lifecycle, offering a comprehensive ecosystem designed to maximize the potential of modern data—from ingestion and storage to processing and analytics—supporting the full lifecycle of AI-driven innovation.

In this blog post, we will cover several AWS use cases for modern data architectures, demonstrating how AWS helps organizations leverage the power of data and generative AI technologies.

---

### Key considerations when choosing a database for your generative AI applications

This blog post focuses on selecting the right database for generative AI applications and provides knowledge that can enhance your understanding, guide decision-making, and ultimately lead to more successful AI projects. Choosing the right database for generative AI applications is not just about storage; it significantly impacts performance, scalability, ease of integration, and the overall efficiency of the AI solution.

![RAG Workflow Diagram](/images/3-BlogsTranslated/Blog_ModernDataArch/img1.jpg)
*Figure 1. Diagram describing the key steps in the RAG workflow*

[Take me to this blog](https://aws.amazon.com/blogs/database/key-considerations-when-choosing-a-database-for-your-generative-ai-applications/)

---

### Strategies for building a data mesh-based enterprise solution on AWS

Adopting a **data mesh** architecture can enhance an organization's effective data management, thereby improving performance, driving innovation, and achieving overall business success. In this guidance, you will explore several strategies for building data mesh solutions on AWS.

![Data Mesh Architecture](/images/3-BlogsTranslated/Blog_ModernDataArch/img2.jpg)
*Figure 2. Data mesh architecture organizes data into domains, where data is treated as quality products to be served to users*

[Take me to this guidance](https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/strategies-for-building-a-data-mesh-based-enterprise-solution-on-aws.html)

---

### Optimizing storage price and performance with Amazon S3

Amazon S3 is an object storage service that supports many use cases, including data architectures. Big data pipelines can use Amazon S3 to store input data, output data, and temporary results. Machine learning systems use Amazon S3 to process application logs and build datasets for both testing and production model training.

Given the importance of this service and the number of use cases a foundational storage service can support, we want to share best practices, performance optimization strategies, and cost optimization strategies when working with Amazon S3. This video shows how **Anthropic** designed their architecture around Amazon S3 in their data architecture.

![S3 Storage Model](/images/3-BlogsTranslated/Blog_ModernDataArch/img3.jpg)
*Figure 3. Workloads with predictable access patterns often have low retrieval rates over a long period, so we can design to apply cheaper storage classes for them.*

[Take me to this video](https://www.youtube.com/watch?v=123456)

> **Note:** If you are curious about the foundational architecture of Amazon S3 and want to dive deeper into its internal design, you can watch the video [Dive deep on Amazon S3](https://www.youtube.com/watch?v=example) from re:Invent.

---

### How HPE Aruba Supply Chain optimized cost and performance by migrating to an AWS modern data architecture

This is an AWS case study on how **HPE Aruba Supply Chain** successfully re-architected and deployed their data solution by adopting a modern data architecture on AWS.

The new solution helped Aruba integrate data from multiple sources while optimizing cost, performance, and scalability. This also enabled Aruba Supply Chain leadership to receive timely insights to make better decisions, thereby enhancing the customer experience.

![HPE Aruba Architecture](/images/3-BlogsTranslated/Blog_ModernDataArch/img4.jpg)
*Figure 4. Reference architecture diagram describing the HPE Aruba Supply Chain architecture, with Amazon S3 as a prominent component.*

[Take me to this blog](https://aws.amazon.com/blogs/big-data/how-hpe-aruba-supply-chain-optimized-cost-and-performance-by-migrating-to-an-aws-modern-data-architecture/)

---

### AWS Modern Data Architecture Immersion Day

This workshop highlights the advantages of adopting a modern data architecture on AWS. By integrating the flexibility of a data lake with specialized analytics services, organizations can significantly enhance their data-driven decision-making capabilities.

We encourage everyone to explore how this architecture can streamline analytics processes and support diverse use cases, from gathering real-time insights to advanced machine learning. This is a great opportunity to leverage modern data architecture.

![Modern Data Architecture Workshop](/images/3-BlogsTranslated/Blog_ModernDataArch/img5.jpg)
*Figure 5. Data architectures are the foundation for powering use cases, from analytics to machine learning.*

[Take me to this workshop](https://catalog.us-east-1.prod.workshops.aws/workshops/example)

---

**See you next time!**

Thanks for reading! In our next blog post, we will share some tips to help you have the best experience working as a developer on AWS. To revisit any previous posts or explore the entire series, visit the [Let’s Architect!](https://aws.amazon.com/architecture/lets-architect/) page.

---

| ![Luca Mezzalira](/images/3-BlogsTranslated/Blog_ModernDataArch/luca.jpg) | **Luca Mezzalira** <br> Luca is a Principal Solutions Architect based in London. He is an author of multiple books and an international speaker. Luca specializes in solutions architecture and has received accolades for revolutionizing front-end architecture scalability using micro-frontends. |
| :--- | :--- |
| ![Federica Ciuffo](/images/3-BlogsTranslated/Blog_ModernDataArch/federica.jpg) | **Federica Ciuffo** <br> Federica is a Solutions Architect at Amazon Web Services. She specializes in container services and is passionate about infrastructure as code. Outside of work, she enjoys reading, drawing, and exploring cuisines. |
| ![Vittorio Denti](/images/3-BlogsTranslated/Blog_ModernDataArch/vittorio.jpg) | **Vittorio Denti** <br> Vittorio Denti is a Machine Learning Engineer at Amazon, based in London. He has a background in distributed systems and machine learning. He is particularly passionate about software engineering and the latest innovations in machine learning science. |
| ![Zamira Jaupaj](/images/3-BlogsTranslated/Blog_ModernDataArch/zamira.jpg) | **Zamira Jaupaj** <br> Zamira is an Enterprise Solutions Architect based in the Netherlands. She is a passionate IT professional with over 10 years of experience designing and implementing complex solutions using containers, serverless, and data analytics. |