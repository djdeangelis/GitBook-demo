# Reporting

## What is Reporting? <a href="#what-is-reporting" id="what-is-reporting"></a>

Reporting automatically generates financial reports and analytics from your payment data. The system creates customizable reports that can be scheduled for delivery to your team members and stakeholders.

Available for all account tiers with additional templates for Enterprise accounts.

## How It Works <a href="#how-it-works" id="how-it-works"></a>

The reporting system processes your transaction data to create:

* Transaction summaries and volume reports
* Revenue and fee breakdowns
* Success rate analytics by region and payment method
* Automated report scheduling and email delivery

## Setup <a href="#setup" id="setup"></a>

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

## Report Types <a href="#report-types" id="report-types"></a>

**Transaction Summary**: Daily, weekly, or monthly transaction volumes and totals **Revenue Report**: Revenue breakdowns with fees and net amounts **Success Rate Analysis**: Payment approval rates by processor and region **Fee Analysis**: Processing costs and fee comparisons

## Configuration Options <a href="#configuration-options" id="configuration-options"></a>

**Output Formats**

* **PDF**: Professional reports with charts and summaries
* **CSV**: Raw data for spreadsheet analysis
* **Email Summary**: Brief overview sent directly via email

**Delivery Options**

* Email delivery to specified recipients
* Download from dashboard
* Scheduled generation (daily, weekly, monthly)

## Monitoring Performance <a href="#monitoring-performance" id="monitoring-performance"></a>

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

## Implementation Best Practices <a href="#implementation-best-practices" id="implementation-best-practices"></a>

**1. Start Simple**

Begin with basic monthly summaries before adding more detailed reports.

**2. Set Clear Recipients**

Ensure reports go to team members who need the information.

**3. Choose Appropriate Frequency**

Match report frequency to business needs - daily for operations, monthly for executives.

**4. Review Regularly**

Check report accuracy and update recipient lists as your team changes.

## Troubleshooting <a href="#troubleshooting" id="troubleshooting"></a>

**Missing Transactions**: Verify the date range matches your expected transaction period.

**Delivery Issues**: Check recipient email addresses and spam folders.

**Empty Reports**: Ensure you have transaction data for the selected time period.

## Getting Support <a href="#getting-support" id="getting-support"></a>

**Setup Help**: Support team assists with initial report configuration **Technical Issues**: Standard support queue for troubleshooting **Custom Reports**: Enterprise accounts can request additional report templates

Reporting helps businesses track payment performance and automate routine financial reporting tasks.
