# Spring Break Camps Analytics Guide

This guide explains everything currently tracked on the Spring Break Camps landing page (`springBreakCamps/index.html`). All data is sent to the `landing_page_analytics` table in Supabase under the page ID `spring_camp_2026`.

## 1. General Engagement
These events track how users interact with the page as a whole.

- **`page_view`**
  - **When it happens:** As soon as the page loads.
  - **What we learn:** How many people visited, where they came from (`referrer`), and what kind of device they are using (`screen_width`).

- **`time_ping`**
  - **When it happens:** Every 30 seconds, *only* if the tab is visible and active.
  - **What we learn:** How long users actually spend reading the page.

- **`click`**
  - **When it happens:** ANY time a user clicks a button or a link anywhere on the page.
  - **What we learn:** General activity levels, which buttons get the most attention (`button_label`), where they navigate (`link_url`), and which section they were in (`section_id`).

---

## 2. Camp Interest & Conversion Setup
These events track how users interact specifically with the camp listings and filters.

- **`filter_change`**
  - **When it happens:** When a user changes the "Location", "Week", "Age", "Experience", or "Interest" dropdowns.
  - **What we learn:** Which options are most popular (`filter_name` -> `selected_value`). *Example: Lots of people searching for "Robotics" in "The Woodlands".*

- **`interest_view`**
  - **When it happens:** When a user clicks "View Details" or "Info" on a specific camp card.
  - **What we learn:** Which camps people want to read more about (`camp` title).

- **`view_details`**
  - **When it happens:** When the camp description pop-up modal officially opens.
  - **What we learn:** Confirmation they actually opened the modal to read about the `camp`.

- **`conversion_intent`**
  - **When it happens:** When a user clicks "Book Now" or "Select" on a camp card.
  - **What we learn:** Strong buying signals. Captures the `camp` title, the button they clicked (`btn_text`), the `price`, and the `schedule` they chose.

- **`faq_expand`**
  - **When it happens:** When a user clicks to open an FAQ question.
  - **What we learn:** What questions parents are most concerned about (`question_text`).

---

## 3. Founder Video Tracking (New!)
These events track how users interact with the new "Talking Head" video from the founder.

- **`founder_video_start`**
  - **When it happens:** When the user clicks the "Click To Unmute" overlay. (Since the video autoplays silently, an unmute counts as a conscious "Start").
  - **What we learn:** How many people actively chose to listen to the message.

- **`founder_video_progress`**
  - **When it happens:** As the video plays, it triggers at exactly 25%, 50%, and 75% completion.
  - **What we learn:** Exactly where people lose interest and stop watching. Supabase receives a `quartile` value (e.g., `'50%'`).

- **`founder_video_pause`**
  - **When it happens:** If the user manually clicks pause while watching.
  - **What we learn:** At what specific second they paused (`current_time` vs `duration`).

- **`founder_video_complete`**
  - **When it happens:** When the user watches the video all the way to 100%.
  - **What we learn:** How many people sat through the entire pitch.

- **`sticky_footer_video_click`**
  - **When it happens:** When a user is scrolling down the page and clicks the floating "Play" button in the sticky footer to jump back up to the video.
  - **What we learn:** How effective that floating sticky play button is at driving traffic to the video.

---

## What about the Lead Capture Pop-up?
The "Enter your email here" pop-up that appears after 15 seconds **does NOT go to Supabase.** 
When a user submits their email, it is sent to a **Google Apps Script** (`script.google.com`) and saved with the tag `LEAD-Spring-Break-Camp-2026`.
