# Projects
<details>
  <summary>2021 - 2022</summary>

  ## AI Trainer
  ### Enhanced Chatbot Capability - Loss Package During Delivery
  - **Background:**
    
  A recurring issue of package loss during courier delivery was identified, primarily due to recipients being unavailable to receive their packages. This problem persisted for several weeks and ranked among the top five operational issues at the time, accounting for approximately 18–20% of total queries received by the chatbot.
  - **Solutions:**
    
  Implemented a chatbot enhancement feature that provides customers with the courier’s phone number during delivery, enabling them to coordinate directly and ensure successful package receipt.

  **Previous Flow:**
  
  Customer asks about their package ⟶ Chatbot asks whether the package has been delivered ⟶ if not, chatbot will inform customer to wait for the ETA ⟶ if yes, chatbot will inform customer to check with family member/neighbor/receptionist ⟶ if the package is still not found, chatbot will switch to live customer service


<img src="https://github.com/adhaafriza/afriza-s_portfolio/blob/main/AIT%20Package%20Loss.jpg" width="1000" height="2000" />

Right after courier handles the package we'll also send them pop-up notifications along with the courier's phone number, and if the customer is tracking their order we'll also send them the courier's information.

**Updated Flow:**

Customer asks about ther package ⟶ Chatbot asks whether the package has been delieverd ⟶ if not, chatbot will inform customer to wait for the ETA ⟶ if yes, chatbot will ask follow-up questions, whether courier handles the package or not ⟶ if not, chatbot will inform customer to wait for the ETA ⟶ if yes, chatbot will give customer courier's phone number

<img src="https://github.com/adhaafriza/afriza-s_portfolio/blob/main/AIT%20Package%20Loss%20NEW.jpg" width="1000" height="2000" />

- **Get the Supporting Data**

To validate the enhancement, we collected supporting data from the `customers_queries` table for the backend team to help prioritize development. Since not all customer queries pass through the chatbot—some can bypass it—capturing data directly from the table ensured comprehensive coverage.

**Source Table: `customers_queries`**

| Column Name | Description |
| ----------- | ----------- |
| Header | Title |
| Paragraph | Text |

</details>
