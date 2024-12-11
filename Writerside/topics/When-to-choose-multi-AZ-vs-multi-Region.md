# When to select multi-AZ vs multi-Region

<tldr>
    <p>This is a question of AWS Best Practices</p>
    <p>AWS Best Practices considers <code>multi-AZ</code> architectures to be <b>highly available</b>.</p>
    <p>Unless <b>specifically required</b>, <code>multi-Region</code> architectures are considered <b>overengineered</b>.</p>
    <p>AWS Best Practices balance availability, scalability, cost, and operational simplicity.</p>
</tldr>

By default if a **highly available architecture** is required, select **`multi-AZ`**.

## When to use `multi-Region`

* The requirement explicitly mentions **regional failures** or requires **disaster recovery**.
* The application is **global**, serving users in multiple regions where reducing latency via proximity routing is a priority.
* The business can tolerate higher costs and complexity for maximum availability.

## Reasoning

AZ Failures are more common that Region Failures.

Protecting from AZ failure is enough to create a highly available architecture.

Protecting from a Regional failure is only by special requirement due to costs, operational overhead, and complex engineering.