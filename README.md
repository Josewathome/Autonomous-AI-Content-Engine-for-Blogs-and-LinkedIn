# Autonomous AI Blog Publishing Workflow

## What this project actually does

This is a fully automated content pipeline that plans, generates, publishes, distributes, and logs technical blog content with almost zero manual intervention after setup.

You give it a topic and a short description once.
It turns that into a multi day content plan.
Then, on a fixed daily schedule, it writes a high quality blog post, publishes it to your blog, announces it on LinkedIn, emails you the result, and makes sure nothing is duplicated or spammy.

This is not a toy script.
It is a production grade AI workflow with state, scheduling, retries, authentication, and failure handling.

## High level architecture

The system is split into two coordinated workflows.

### Workflow 1 Content planning and scheduling

This workflow is triggered manually.

Input

* A core topic such as Django Models
* A short description of what the topic should cover

What it does

* Uses AI to break the topic into a structured multi day content plan
* Generates five distinct sub topics intended for daily publishing
* Stores the plan and metadata in Supabase
* Marks the workflow as active and ready for execution

Output

* A persisted five day content plan
* No publishing happens here

### Workflow 2 Daily autonomous publishing

This workflow runs automatically every 24 hours.

What it does step by step

1. Checks timing rules to avoid spam

   * Verifies no blog has been published in the last few hours
   * Ensures the same topic is never published twice

2. Selects the next unpublished item from the stored plan

3. Research and content generation

   * Searches the web for up to date and relevant information
   * Combines external sources with the original user provided context
   * Generates a long form technical blog post
   * Saves the content as Markdown

4. Image generation

   * Generates a topic relevant image using GPT image generation
   * Receives an image URL rather than binary data
   * Attempts to download the image for downstream use

5. Blog publishing

   * Authenticates against the blog platform using Bearer token authorization
   * Publishes the Markdown content via a custom publishing endpoint
   * Logs the published URL and timestamp

6. LinkedIn distribution with fallback logic

   * Generates a LinkedIn post announcing the new blog
   * Attempts to publish with the generated image
   * If image download fails, publishes text only instead
   * Ensures LinkedIn is always updated even if media handling fails

7. Notifications

   * Sends an email confirmation after successful publishing
   * Includes confirmation of blog and LinkedIn post creation

8. State persistence

   * Updates Supabase to mark the content item as published
   * Prevents duplicate posts or overlapping executions

## Failure handling and safeguards

This workflow is defensive by design.

Built in protections include

* Retry logic when AI responses fail or return invalid output
* Time based locks to prevent multiple posts in short windows
* Duplicate detection across all previously published content
* Graceful degradation when image download or media upload fails

The system always prefers partial success over total failure.
A blog post and LinkedIn announcement will still go out even if image handling breaks.

## Tech stack

* AI content generation using GPT models
* Image generation using GPT image APIs
* Supabase for workflow state and persistence
* Custom blog publishing API with Bearer authentication
* LinkedIn API for automated posting
* Gmail for notification emails
* Google Sheets for auxiliary logging and configuration

## Setup requirements

To run this workflow, you must configure the following

* AI provider API key
* Supabase project credentials
* Blog platform Bearer token
* LinkedIn OAuth authorization
* Gmail authorization
* Google Sheets authorization

All external services must be authenticated before execution.

## How to use

1. Configure all required API keys and authorizations
2. Trigger Workflow 1 with a topic and description
3. Walk away

From that point on, the system publishes one blog per day automatically until the plan is complete.

## Who this is for

This project is for

* Engineers who want consistent technical content without manual writing
* Founders building personal or company thought leadership
* Anyone tired of writing blogs but still wanting quality output

If you want full control and zero automation, this is not for you.
If you want leverage, this is exactly that.

## Project status

Actively used in production.
Not a demo.
Not experimental.
