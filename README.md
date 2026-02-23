# 🧱 Designing a Data Warehouse with Medallion Architecture in Microsoft Fabric

---
<img width="900" height="579" alt="image" src="https://github.com/user-attachments/assets/107a72e2-6859-4933-9403-fe01b7a796f9" />

## <span style="color:#D35400;">Problem</span>

Data warehouses are essential components of modern analytics systems, offering optimized storage and processing capabilities for large volumes of data.

When integrated with a **Lakehouse architecture**, you can combine the best of both worlds:

- <span style="color:#1F618D;"><b>Structured, schema-enforced storage</b></span> (data warehouse strengths)
- <span style="color:#117A65;"><b>Flexibility + scalability</b></span> (data lake strengths)

**Microsoft Fabric** provides an excellent environment for implementing the **Medallion Architecture**—a design pattern for building efficient data processing pipelines by layering data into **bronze**, **silver**, and **gold** zones.

---

## <span style="color:#2E86C1;">Solution</span>

In this article, I will outline an **end-to-end approach** to designing a data warehouse using **Medallion Architecture** in **Microsoft Fabric**.

---

## <span style="color:#6C3483;">What is the Medallion Architecture?</span>

Without going into the nitty-gritty of what a data warehouse entails, I will describe the **Medallion Architecture**, an approach we will leverage in this article, and best practices on leveraging it.

The **Medallion Architecture** is a design principle that organizes data into **three primary layers**, where each layer progressively refines the data and improves usability.

---

## <span style="color:#7D6608;">Medallion Layers</span>

<table>
  <thead>
    <tr>
      <th align="left">Layer</th>
      <th align="left">Purpose</th>
      <th align="left">What you typically see</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>
        <span style="background:#CD7F32;color:white;padding:4px 10px;border-radius:999px;"><b>Bronze</b></span>
      </td>
      <td><b>Raw ingestion</b> from various sources</td>
      <td>Errors, duplicates, inconsistencies, unfiltered records</td>
    </tr>
    <tr>
      <td>
        <span style="background:#C0C0C0;color:#111;padding:4px 10px;border-radius:999px;"><b>Silver</b></span>
      </td>
      <td><b>Clean + transform</b> into analysis-ready data</td>
      <td>Standardized schemas, validated fields, conformed dimensions; usable for analytics & ML</td>
    </tr>
    <tr>
      <td>
        <span style="background:#D4AF37;color:#111;padding:4px 10px;border-radius:999px;"><b>Gold</b></span>
      </td>
      <td><b>Curate + aggregate</b> for business consumption</td>
      <td>Highly curated datasets optimized for BI reports, dashboards, and KPIs</td>
    </tr>
  </tbody>
</table>

---

## <span style="color:#1B4F72;">How the layers work together</span>

Each layer refines the data, ensuring that **data quality** and **transformation logic** are applied as the data progresses:

<span style="background:#CD7F32;color:white;padding:4px 10px;border-radius:999px;"><b>Bronze</b></span>
&nbsp;➡️&nbsp;
<span style="background:#C0C0C0;color:#111;padding:4px 10px;border-radius:999px;"><b>Silver</b></span>
&nbsp;➡️&nbsp;
<span style="background:#D4AF37;color:#111;padding:4px 10px;border-radius:999px;"><b>Gold</b></span>

From the raw **bronze** stage to the analytical **gold** stage, data becomes increasingly reliable, standardized, and ready for decision-making.

Setting Up the Medallion Architecture in Microsoft Fabric
To set up the medallion architecture to create a data warehouse, I will leverage two Lakehouses (Bronze and Silver layers) and a Warehouse (Gold layer). Note: There is a difference between a Lakehouse and a Warehouse regarding Microsoft Fabric. See this article by Microsoft for details.

A summary of this approach is outlined in the architecture diagram below.

<img width="900" height="579" alt="image" src="https://github.com/user-attachments/assets/c80ccdaa-cea6-48b3-b246-274410114070" />

# Medallion Architecture Setup in Microsoft Fabric

For this article, I will use the following steps to set up the medallion architecture for developing the data warehouse in Microsoft Fabric.

## Overview of Steps

1. **Create the two Fabric Lakehouses (Bronze and Silver)**
2. **Ingest raw data into the bronze Lakehouse layer**
3. **Transform data in the silver layer**
4. **Curate data in the Gold (Data Warehouse) layer**
5. **Set up and schedule the pipelines** for orchestrating data between the layers
6. **Enable data access for reporting**

---

## Step 1: Create Two Fabric Lakehouses (Bronze and Silver)

We will start by creating a Lakehouse in Microsoft Fabric as the foundation of the data architecture. The Lakehouse serves as the central repository for 
storing structured, semi-structured, and unstructured data using Fabric’s OneLake for scalable and distributed data storage.

> [!IMPORTANT]
> First, we need to create a **Workspace** in [app.powerbi.com](https://app.powerbi.com).
> **Note:** Creating workspaces in the Power BI service requires administrative rights to the tenant. Check with your Power BI admin to set up a workspace 
for this purpose.

For this demo, I have created a workspace called `fab_ws_sales`. The process below shows one way to create a Lakehouse in Microsoft Fabric.


