---
nav_title: Your Partner Page
page_order: 1

description: "This is the Google Search and SEO description that will appear, try to make this informative and concise, yet brief."
alias: /partners/your_partner_name/

page_type: partner
hidden: true
---

# Tinyclues

> [Tinyclues](https://www.tinyclues.com) is an Audience Building feature that offers the capability to increase the number of campaigns and revenue without harming customer experience along with analytics to track the performance of CRM campaigns both online & offline

Together, Braze and Tinyclues offer our clients a path to better CRM planning and strategy. 
Through our partnership, clients can send more targeting campaigns, find new product opportunities, and elevate revenue using an incredibly user-friendly UI.

## Requirements

* An active Tinyclues account
* An active Braze account with the ability to use Tinyclues integration
* The API key corresponding to Tincylues’ integration must be communicated to your Tinyclues Data Operation representative in order to set up the integration

| Requirement | Origin | Access | Description |
|---|---|---|---|
| Braze API Key | Braze | You will need to create a new API Key.<br><br>This can be created in the __Developer Console -> API Settings -> Create New API Key__ with __users.track__ permissions. | This description should tell you what to do with the Braze API Key. |
| Braze REST Endpoint | Braze | [Braze REST Endpoint List][1] | Your REST Endpoint URL. Your endpoint will depend on the Braze URL for your instance. |
{: .reset-td-br-1 .reset-td-br-2 .reset-td-br-3  .reset-td-br-4}

## Integration

This is where you break down the integration into steps. Do not just write endless paragraphs - these are technical documents that will be used by marketers and developers alike to get the integration up and running. Your only goal for this section is to write descriptive documentation that helps the Braze User get the job done. By 'Type of Integration' in the section title, we mean to indicate whether or not this is a Side-by-Side integration, server-to-server, or Out-of-the-Box. This enables you to have multiple Integration Sections if there is more than one way to integrate with this partner.

### Step 1: This Is a Short Description of Step One

Just break this down, including any code as necessary. Remember that you can offer several different sets of code - there's no need to only offer one way to integrate.

### Step 2: This Step Will Describe Images

You have the option to put images in your documentation, so we recommend you do and do so mindfully.

### Step 3: How Many Steps

Outline thorough usage of the integration - especially if it means inserting Liquid into our message composer.

## Customization

This is an __optional__ section. Here, you could outline any specific ways to customize your integration between the two partners.

## Using This Integration

### Step 1: Export a Campaign from Tinyclues Platform

Each time you want to activate a given campaign through Braze, you’ll have to first export it from Tinyclues platform.  
From Tinyclues UI, select the campaign(s) you want to export and click on export.

![campaign_export][2]

## Use Cases

This can be a critical part of your documentation. Though this is optional, this is a good place to outline typical or even novel use cases for the integration. This can be used as a way to sell or upsell the relationship - it provides context, ideas, and most importantly a way to visualize the capabilities of the integration.

[1]: {{site.baseurl}}/developer_guide/rest_api/basics/#endpoints)  
[2]: {% image_buster /assets/img/tinyclues/TC_Braze1.png %}
