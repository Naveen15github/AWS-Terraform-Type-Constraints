# AWS-Terraform-Type-Constraints

This document represents my consolidated understanding of **Terraform type constraints**, captured through hands-on practice and reinforced with insights from a detailed tutorial video. It covers every major variable type, real-world use cases, validation techniques, and common pitfalls—serving as an end-to-end reference for designing reliable Terraform modules.

---

## **📘 Overview**

Terraform variables fall into two broad categories:

1. **Based on Purpose**

   * **Input Variables** – Accept user-supplied values
   * **Output Variables** – Expose values after resource creation
   * **Local Values** – Define intermediate expressions

2. **Based on Value / Constraint**

   * **Primitive Types**
   * **Complex Types**
   * **Dynamic Types (null, any)**

This README focuses on **Type Constraints**, showing how Terraform enforces structure, validates input, and prevents misconfigurations.

---

# **1. Primitive Types**

Primitive types are simple data types used to store basic values.

### **String** *(0:59 – 1:33)*

Used for text input enclosed in double quotes.

```hcl
variable "env" {
  type = string
}
```

### **Number** *(1:48 – 1:59)*

Represents any numeric value (integer or float).

```hcl
variable "instance_count" {
  type = number
}
```

### **Boolean** *(2:03)*

Used for true/false conditions.

```hcl
variable "enable_logging" {
  type = bool
}
```

---

# **2. Complex Types**

Complex types store multiple values and are heavily used in real-world Terraform modules.

---

## **List** *(2:48 – 2:56, 21:58, 23:37, 50:58)*

* Ordered collection
* Allows duplicate values
* Elements accessed via index

```hcl
variable "az_list" {
  type = list(string)
}
```

Access:

```hcl
var.az_list[0]
```

---

## **Set** *(2:50, 32:28, 33:50)*

* Unordered
* Ensures unique values
* Cannot be accessed via index directly
* Must convert with `tolist()`

```hcl
variable "unique_sgs" {
  type = set(string)
}
```

Access:

```hcl
tolist(var.unique_sgs)[0]
```

---

## **Map** *(2:52, 38:08, 50:38)*

* Key-value pairs
* Keys are strings
* Values must be the same type

```hcl
variable "app_tags" {
  type = map(string)
}
```

Access:

```hcl
var.app_tags["Name"]
```

---

## **Tuple** *(2:53, 43:41, 45:36, 49:09)*

* Ordered
* Allows different data types
* Type & sequence matter

```hcl
variable "mixed_config" {
  type = tuple([string, number, bool])
}
```

Access:

```hcl
var.mixed_config[1]
```

---

## **Object** *(46:47 – 50:21)*

* Structure with named attributes
* Each attribute has its own type

```hcl
variable "server_config" {
  type = object({
    name       = string
    cpu_cores  = number
    enable_tls = bool
  })
}
```

Access:

```hcl
var.server_config.name
```

---

# **3. Dynamic Types**

## **Null** *(2:58)*

Represents an undefined value.

Usage example:

```hcl
variable "optional_tag" {
  type    = string
  default = null
}
```

## **Any** *(3:21 – 10:25)*

If no type is specified, Terraform infers it based on input.

```hcl
variable "auto_detect" {
  type = any
}
```

---

# **4. Validation Blocks & Constraints**

Validation ensures only valid data enters your module.

```hcl
variable "replicas" {
  type = number

  validation {
    condition     = var.replicas >= 1 && var.replicas <= 5
    error_message = "Replicas must be between 1 and 5."
  }
}
```

---

# **5. Real-World Patterns & Practical Usage**

### **Environment-Specific Configurations**

```hcl
variable "env_config" {
  type = map(object({
    instance_type = string
    scaling_limit = number
  }))
}
```

### **Tag Standardization**

```hcl
variable "standard_tags" {
  type = map(string)
}
```

### **Network Configuration Validation**

```hcl
variable "network" {
  type = object({
    vpc_cidr = string
    subnets  = list(string)
  })
}
```

### **Security Policy Enforcement**

```hcl
variable "ingress_rules" {
  type = list(object({
    port     = number
    protocol = string
  }))
}
```

---

# **6. Common Mistakes & Troubleshooting**

* Trying to access **set elements by index** → must convert with `tolist()`
* Using **variables inside variable definitions** incorrectly
* Mismatch between **expected type** and **provided value**
* Incorrect handling of tuples due to strict type sequence
* Passing numbers as strings

---

# **7. Best Practices**

* Always explicitly specify variable types
* Use validation blocks for business rules
* Use `list` when order matters; use `set` when uniqueness matters
* Document type expectations in descriptions
* Prefer `object` over deeply nested maps
* Convert values safely using `tolist()`, `tostring()`, `tonumber()`
* Validate complex inputs thoroughly

---

## **📌 Conclusion**

This guide consolidates my learning and implementation experience with Terraform type constraints. It covers everything from fundamental primitive types to advanced object structures, ensuring clean, predictable, and fail-safe infrastructure definitions. These concepts form the backbone of writing scalable, modular, and production-ready Terraform code.

