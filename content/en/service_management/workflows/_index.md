---
title: Workflows
kind: documentation
disable_toc: false
aliases:
- /workflows
further_reading:
- link: "https://www.datadoghq.com/blog/automate-end-to-end-processes-with-datadog-workflows/"
  tag: "Blog"
  text: "Automate end-to-end processes and quickly respond to events with Datadog Workflows"
- link: "https://www.datadoghq.com/blog/automate-security-tasks-with-workflows-and-cloud-siem/"
  tag: "blog"
  text: "Automate common security tasks and stay ahead of threats with Datadog Workflows and Cloud SIEM"
---

{{< img src="service_management/workflows/hero.jpg" alt="Graphic showing three purposes of Workflows: Orchestrate, Automate, and Break down Silos" >}}

Datadog Workflows allow you to orchestrate and automate your end-to-end processes. Build workflows made up of [actions][1] that connect to your infrastructure and tools. These actions can also perform data and logical operations, allowing you to build complex flows with branches, decisions, and data operations.

## Configure workflow actions

Datadog Workflows provide over 400+ actions across several tools, along with Workflow-specific actions such as the HTTP action and the JavaScript data operator. These actions allow you to perform any task required in your flow.

## Start with blueprints

Datadog provides you with preconfigured flows in the form of out of the box [blueprints][2]. Dozens of blueprints help you build processes around incident management, DevOps, change management, security, and remediation.

## Automate critical tasks

Trigger your workflows from monitors, security signals, or dashboards, or trigger them manually. This flexibility allows you to respond with the appropriate workflow at the point you become aware of an issue affecting the health of your system. Automating critical tasks with Datadog Workflows helps keep your systems up and running by improving the time to resolution and reducing the possibility of errors.

## Examples

Below are a few examples of workflows you can build:
- Automate scaling of your AWS Auto Scaling Groups when monitors tracking critical metrics of these Auto Scaling Groups go into the alert state.
- Automatically create investigative notebooks of malicious IPs to be detected by Security Signals, and then block these IPs in CloudFlare with the click of a button.
- Execute workflows to roll back to stable versions of your application directly from the Dashboards you use to track the health of your systems.
- Manage feature flags by automatically updating your feature flag config files in GitHub and automating the pull request and merge process.

Watch the video below to see a workflow configured to redeploy a Lambda function when it experiences a high error rate.

{{< wistia 0klmggfhaf>}}

## Further reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: /service_management/workflows/actions_catalog/
[2]: /workflows/build/#build-a-workflow-from-a-blueprint
