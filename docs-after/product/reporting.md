---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/N5k8pHgqDB1McsDpSWlO/product/reporting
---

# Reporting

## What is Reporting? <a href="#what-is-reporting" id="what-is-reporting"></a>

Reporting automatically generates financial reports and analytics from your payment data. The system creates customizable reports that can be scheduled for delivery to your team members and stakeholders.

Instead of manually exporting spreadsheets and building the same charts every week or month, Reporting turns your raw transaction data into repeatable, shareable summaries that arrive on a schedule you control.

Available for all account tiers with additional templates for Enterprise accounts.

## How It Works <a href="#how-it-works" id="how-it-works"></a>

The reporting system processes your transaction data to create:

* Transaction summaries and volume reports
* Revenue and fee breakdowns
* Success rate analytics by region and payment method
* Automated report scheduling and email delivery

Most finance and operations teams use these reports to answer recurring questions: How much did we process last month?, What are our fees by processor?, or Where are approval rates dropping?

## Setup <a href="#setup" id="setup"></a>

Setting up Reporting typically involves three decisions: which reports you need, how often they should run, and who should receive them.

**1. Enable Reporting**

Navigate to Analytics > Reporting in your dashboard and click "Enable Reports".

**2. Configure Report Templates**

Choose from available report types:

```
// Example report configuration
const reportConfig = {
  template: 'monthly_summary',
  frequency: 'monthly',
  format: 'pdf',
  recipients: ['finance@company.com'],
  dateRange: 'last_month'
};
```

**3. Schedule Reports**

Set up automated report delivery:

```
const report = await evolve.reporting.schedule({
  templateId: 'monthly_summary',
  frequency: 'monthly',
  deliveryDay: 1, // First day of month
  recipients: ['cfo@company.com', 'accounting@company.com'],
  format: 'pdf'
});
```

Once scheduling is in place, your team can rely on reports arriving automaticallyfreeing them from manual exports and ad-hoc requests.

## Report Types <a href="#report-types" id="report-types"></a>

Each report type answers a different class of questions about your payments.

* **Transaction Summary**: Daily, weekly, or monthly transaction volumes and totals
* **Revenue Report**: Revenue breakdowns with fees and net amounts
* **Success Rate Analysis**: Payment approval rates by processor and region
* **Fee Analysis**: Processing costs and fee comparisons

You can start with a small set of core reports for leadership and operations, then expand into more specialized templates as your reporting needs mature.

## Configuration Options <a href="#configuration-options" id="configuration-options"></a>

Tune Reporting so that each audience receives information in the format that works best for them.

**Output Formats**

* **PDF**: Professional reports with charts and summaries
* **CSV**: Raw data for spreadsheet analysis
* **Email Summary**: Brief overview sent directly via email

**Delivery Options**

* Email delivery to specified recipients
* Download from dashboard
* Scheduled generation (daily, weekly, monthly)

## Monitoring Performance <a href="#monitoring-performance" id="monitoring-performance"></a>

Once reports are running, you can monitor how theyre being used and whether delivery is working as expected.

**Dashboard**

View recent reports and generation status:

* Report history and download links
* Delivery status and recipient confirmations
* Data coverage and last update times

**Report Analytics**

Track report usage:

* Most downloaded report types
* Delivery success rates
* Data freshness indicators

These analytics help you refine which reports are truly valuable and which can be retired or consolidated.

## Implementation Best Practices <a href="#implementation-best-practices" id="implementation-best-practices"></a>

**1. Start Simple**

Begin with basic monthly summaries before adding more detailed reports. This keeps the initial rollout manageable and avoids overwhelming stakeholders.

**2. Set Clear Recipients**

Ensure reports go to team members who need the information. For example, finance may want full CSV exports while executives prefer concise PDF summaries.

**3. Choose Appropriate Frequency**

Match report frequency to business needs - daily for operations, monthly for executives. Too-frequent reports can lead to alert fatigue, while infrequent ones can leave teams in the dark.

**4. Review Regularly**

Check report accuracy and update recipient lists as your team changes. Periodic reviews help keep your reporting setup aligned with how your business actually operates.

## Troubleshooting <a href="#troubleshooting" id="troubleshooting"></a>

**Missing Transactions**: Verify the date range matches your expected transaction period.

**Delivery Issues**: Check recipient email addresses and spam folders.

**Empty Reports**: Ensure you have transaction data for the selected time period.

If problems continue, capture the report name, expected delivery time, and example recipients before contacting support.

## Getting Support <a href="#getting-support" id="getting-support"></a>

**Setup Help**: Support team assists with initial report configuration

**Technical Issues**: Standard support queue for troubleshooting

**Custom Reports**: Enterprise accounts can request additional report templates

Reporting helps businesses track payment performance and automate routine financial reporting tasks so teams can focus more on analysis and decision-making, and less on manual data collection.
