# Usage insights

## What is Usage Insights? <a href="#what-is-usage-insights" id="what-is-usage-insights"></a>

Usage Insights provides visibility into how your team uses the payment platform. The system tracks API calls, dashboard activity, and feature adoption to help optimize your account setup and identify training opportunities.

Available for all account tiers with enhanced metrics for Enterprise accounts.

## How It Works <a href="#how-it-works" id="how-it-works"></a>

The insights system monitors your platform usage across:

* API endpoint usage and response times
* Dashboard page views and user activity
* Feature adoption and utilization rates
* Team member access patterns and permissions

## Setup <a href="#setup" id="setup"></a>

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

## Insight Types <a href="#insight-types" id="insight-types"></a>

**API Usage**: Request volumes, endpoint popularity, and error rates **Dashboard Activity**: Page views, session duration, and feature clicks **Team Performance**: User activity levels and login patterns **Feature Adoption**: Which tools are used most and least frequently

## Configuration Options <a href="#configuration-options" id="configuration-options"></a>

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

**Usage Dashboard**

View key metrics and trends:

* Daily active users and session counts
* Most popular features and API endpoints
* Usage patterns by time of day and week
* Error rates and performance bottlenecks

**Weekly Summaries**

Automated insights delivered via email:

* Usage highlights and changes from previous week
* Underutilized features and optimization suggestions
* Team activity summaries and adoption metrics

## Implementation Best Practices <a href="#implementation-best-practices" id="implementation-best-practices"></a>

**1. Start with Team Level**

Begin with aggregate tracking before enabling individual monitoring.

**2. Review Weekly**

Check usage patterns regularly to identify optimization opportunities.

**3. Focus on Adoption**

Use insights to improve onboarding and feature discovery.

**4. Respect Privacy**

Be transparent about tracking and provide opt-out options when appropriate.

## Troubleshooting <a href="#troubleshooting" id="troubleshooting"></a>

**No Data Showing**: Verify tracking is enabled and wait 24 hours for initial data collection.

**Missing Team Members**: Check that users have proper permissions and have logged in recently.

**API Metrics Empty**: Ensure your integration is making API calls with valid authentication.

## Getting Support <a href="#getting-support" id="getting-support"></a>

**Setup Assistance**: Support team helps configure tracking preferences **Data Questions**: Account managers explain usage patterns and recommendations **Privacy Concerns**: Security team addresses data handling and retention policies

Usage Insights helps teams optimize their payment platform usage and improve operational efficiency.
