# terraform

# Terraform

## File Structure

Terraform uses a 

```terraform
provider "<provider>" {
  // provider settings  
}

resource "<provider>_<resource_type>" "name" {
  // config options... as key/value pairs
}
```