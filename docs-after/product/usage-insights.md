---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/N5k8pHgqDB1McsDpSWlO/product/usage-insights
---

# Usage insights

## What is Usage Insights? <a href="#what-is-usage-insights" id="what-is-usage-insights"></a>

Usage Insights provides visibility into how your team uses the payment platform. The system tracks API calls, dashboard activity, and feature adoption to help optimize your account setup and identify training opportunities.

Instead of guessing which features are used or where teams are getting stuck, you can see a clear picture of behavior: who is logging in, which APIs are called most often, and which parts of the product are being ignored. That makes it easier to decide where to invest in onboarding, documentation, or process changes.

Available for all account tiers with enhanced metrics for Enterprise accounts.

## How It Works <a href="#how-it-works" id="how-it-works"></a>

The insights system monitors your platform usage across:

* API endpoint usage and response times
* Dashboard page views and user activity
* Feature adoption and utilization rates
* Team member access patterns and permissions

Think of it as an activity layer sitting on top of your payments data: while Reporting tells you what is happening with your revenue, Usage Insights tells you how your team is using the tools that drive that revenue.

## Setup <a href="#setup" id="setup"></a>

Getting started with Usage Insights usually takes just a few minutes. First you enable tracking, then you decide how granular you want the data to be and who should see it.

**1. Enable Usage Insights**

Navigate to Account > Usage Insights in your dashboard and click "Enable Tracking".

**2. Configure Tracking Preferences**

Set your monitoring scope:

```
// Example insights configuration
const insightsConfig = {
  trackingLevel: 'team', // or 'individual', 'department'
  includeAPI: true,
  includeDashboard: true,
  retentionPeriod: '90_days',
  anonymizeData: false
};
```

**3. Set Up Team Access**

Define who can view usage data:

```
const access = await evolve.insights.configureAccess({
  viewers: ['admin@company.com', 'ops@company.com'],
  scope: 'full', // or 'limited'
  departments: ['finance', 'engineering']
});
```

As a starting point, many teams grant full access to admins and operations leads, then gradually extend visibility to managers who are responsible for adoption in their own departments.

## Insight Types <a href="#insight-types" id="insight-types"></a>

Usage Insights surfaces several types of signals. Together, they provide a narrative of how your organization is using Evolve Payments over time.

* **API Usage**\
  Request volumes, endpoint popularity, and error rates help developers understand how integrations behave in production and where reliability needs to improve.
* **Dashboard Activity**\
  Page views, session duration, and feature clicks show which parts of the product your team relies on most, and which areas might need better training or documentation.
* **Team Performance**\
  User activity levels and login patterns highlight which teams are actively managing payments and which may be falling behind on reviews or reconciliations.
* **Feature Adoption**\
  Insights into which tools are used most and least frequently help you prioritize rollouts, deprecate unused features, and measure the impact of new launches.

## Configuration Options <a href="#configuration-options" id="configuration-options"></a>

Configure Usage Insights to match your privacy requirements and reporting needs.

**Tracking Scope**

* **Team Level**: Aggregate usage across all team members
* **Individual Level**: Per-user activity tracking
* **Department Level**: Usage grouped by team departments

**Data Retention**

* 30 days: Basic usage patterns
* 90 days: Trend analysis and comparisons
* 1 year: Long-term adoption tracking (Enterprise only)

**Privacy Settings**

* Anonymized tracking for sensitive teams
* Opt-out options for individual users
* Data export controls and access logs

## Monitoring Performance <a href="#monitoring-performance" id="monitoring-performance"></a>

Once tracking is enabled, most teams make Usage Insights part of a weekly or monthly review routine.

**Usage Dashboard**

View key metrics and trends:

* Daily active users and session counts
* Most popular features and API endpoints
* Usage patterns by time of day and week
* Error rates and performance bottlenecks

You can quickly answer questions like Did adoption go up after our last training? or Which team is driving most of the API traffic?

**Weekly Summaries**

Automated insights delivered via email:

* Usage highlights and changes from previous week
* Underutilized features and optimization suggestions
* Team activity summaries and adoption metrics

These summaries are especially useful for managers who want to stay informed without logging into the dashboard every day.

## Implementation Best Practices <a href="#implementation-best-practices" id="implementation-best-practices"></a>

**1. Start with Team Level**

Begin with aggregate tracking before enabling individual monitoring. This gives you a high-level picture without overloading stakeholders with detail.

**2. Review Weekly**

Check usage patterns regularly to identify optimization opportunities. For example, if a new reporting feature isnt being used after launch, you may need an internal announcement or short training.

**3. Focus on Adoption**

Use insights to improve onboarding and feature discovery. High-value features that are rarely used often represent quick wins for efficiency.

**4. Respect Privacy**

Be transparent about tracking and provide opt-out options when appropriate. Clear communication builds trust and encourages teams to use the data constructively.

## Troubleshooting <a href="#troubleshooting" id="troubleshooting"></a>

**No Data Showing**: Verify tracking is enabled and wait 24 hours for initial data collection.

**Missing Team Members**: Check that users have proper permissions and have logged in recently.

**API Metrics Empty**: Ensure your integration is making API calls with valid authentication.

If issues persist, capture a recent example (timestamp, user, expected activity) before contacting supportthis context helps resolve problems faster.

## Getting Support <a href="#getting-support" id="getting-support"></a>

**Setup Assistance**: Support team helps configure tracking preferences

**Data Questions**: Account managers explain usage patterns and recommendations

**Privacy Concerns**: Security team addresses data handling and retention policies

Usage Insights helps teams not just see what the product can do, but how it is actually being usedso you can make informed decisions about training, process, and product configuration.
