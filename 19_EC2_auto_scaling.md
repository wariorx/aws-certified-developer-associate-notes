## Autoscaling
* Adding additional servers to handle capacity and reachability issues
* **Vertical scaling**: Manual work...increase instance size; good for active-passive systems where the active instance might become overwhelmed
  * Stop passive instance... limited by size options available
  * Change instance size or type
  * Shift traffic to the NEW passive instance, making it active
  * Stop, change size, and start the new passive instance (previous passive)
* **Horizontal Scaling**: Add additional instances; ideal for active-active systems
* Autoscaling can be undone when no longer required by performing the same operations in reverse (IE remove instances or downsize them)

### EC2 Auto-scaling
* **Automatic scaling**
* **Scheduled scaling**
* **Fleet maangement**: automatically replace unhealthy instances
* **Predictive scaling** use ML to help schedule optimum number of instances
* **Purshcase options** multiple models, instance types, and AZ options

* **ELB** integrates seamlessly into EC2 auto-scalling by notifying the ELB asa an isntance is addded or removed. To become active, and instnace MUSt pass health checks

## Configuring auto-scaling requirements

### Launch Template configuration
* Parameters specified when creating an EC2 instance (AMI ID, instance type, security group, EBS volumes, etc.) are stored in launch templates
* Re-use launchtemplates manually or for EC2 auto-scalling
* Version templates for rollbacks or version specifications
* Templates can be created:
  * From an existing EC2 instance
  * From an already existing template or previous template version
  * From scratch; AMI ID, instance type, key pair, security group, storage, resource tags
* **Launch configurations**: similar to launch templates but cannot be created from a previous template or existing EC2; hence not recommended

### Auto-scaling groups
* Define WHERE EC2 auto-scaling will deploy the new resources to
  * VPC
  * Subnets: select at least 2 for multi-AZ availability
* Specify the type of **purchase** for the EC@ instances; 
  * On-demand
  * Spot
  * Combination of the two
* Configure capacity:
  * **Minimum Capacity**: minimum number of instances running in the group, even if lowering threshold is rached. Often a minimum of 2 is recommended
  * **Desired Capacicy**: Number of EC2 isntances that EC2 auto-scaling creates at teh time the group is created. Must be min <= des <= max. If desired decreases, EC2 removes the oldest instance
  * **Maximum Capacity**: maximum number of instances running, even if the threshold for adding more is reached. Growth increases as instances are added.

### Scaling Policies
* by default, desired capacity is kept
* **Cloudwatch metrics and alarms** can be used to trigger auto-scaling based on attributes such as CPU usage.
* Types
  * **Simple**: do exactly as described (add/remove instances, update desired capacity, etc.)
    * Enters a cooldown period after being invoked
  * **Step**: respond to additional alarms when a scaling activity or health check replacement is in progress; IE add 2 instances at 80% CPU; then 4 more at 90% CPU if needed
  * **Target tracking**: provide a target value to track and automatically creates the required cloudwatch alarms you need to apply policies to.