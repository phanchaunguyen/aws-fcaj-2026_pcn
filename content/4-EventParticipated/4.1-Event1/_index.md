---
title: "Event 1"
date: 2026-06-27
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

![Mô tả cho ảnh](/images/4-Event/1.png)

### Event Information

| Field | Details |
|-------|---------|
| **Event Name** | URL Shortener | Culture fit in corporate environment |
| **Date & Time** | 09:00, June 13, 2026 |
| **Location** | On-site |
| **Role** | Attendee |

---

#### 1. Building a Scalable URL Shortener on AWS
*Speaker: Cloud Architecture Team*

This session explored the business and technical motivations behind building a custom URL shortener (similar to bit.ly). The speaker explained that the primary purposes of a URL shortener include link management, tracking click analytics, preserving character limits in SMS or social media, and enhancing brand identity with custom domains. 

To achieve this on the AWS Cloud, the presentation outlined a highly scalable, serverless architecture:
*   **Amazon API Gateway:** Acts as the front door to receive user requests (e.g., clicking the short link).
*   **AWS Lambda:** Contains the backend logic to look up the short code and issue an HTTP 301/302 redirect to the user's browser.
*   **Amazon DynamoDB:** Serves as a fast, low-latency NoSQL database storing the key-value mapping between the generated short code and the original long URL.
*   **Amazon Route 53:** Manages the DNS routing for the custom short domain name.

#### 2. The Critical Role of Culture Fit in Large Corporations
*Speaker: HR & Engineering Leadership*

Transitioning from technical skills to career development, this presentation highlighted that technical proficiency alone is no longer enough to succeed in large corporations; "culture fit" is equally, if not more, important. 

The speaker emphasized that a toxic or mismatched work environment can quickly lead to burnout, regardless of how good the salary or tech stack is. The core advice for candidates was to conduct deep due diligence *before* attending an interview. Strategies discussed included:
*   **Insider Networking:** Reaching out to current or former employees (e.g., via LinkedIn) to ask candid questions about work-life balance, management styles, and daily pressures.
*   **Reputable Review Platforms:** Thoroughly researching the company on trusted sites (like Glassdoor or Blind) to identify recurring red flags or positive cultural traits.
*   **Reverse Interviewing:** Treating the interview as a two-way street to assess if the company's core values genuinely align with your own working style.

---

### Outcomes and Value Gained

- **Serverless Architecture Mastery:** The URL shortener breakdown provided a perfect, real-world use case for the API Gateway + Lambda + DynamoDB triad. This serverless pattern is highly applicable to our own projects, teaching us how to handle high-read traffic with minimal infrastructure management and low costs.

- **Understanding Business Value in Cloud Engineering:** Learning *why* URL shorteners exist (analytics, branding, user experience) reinforced the idea that cloud architecture must always serve a clear business purpose, rather than just being an exercise in utilizing cool technology.

- **Strategic Career Planning:** The culture fit session completely reframed my approach to job hunting. It highlighted the necessity of looking beyond the job description and compensation package to evaluate the actual working environment.

- **Proactive Interview Preparation:** I now have a clear, actionable checklist before interviewing with large enterprises: I must actively seek out "insider" perspectives and read verified reviews to ensure the company's culture aligns with my long-term career goals and mental well-being.