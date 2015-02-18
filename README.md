# Terraform Module - Digital Ocean Droplet

Specifies an opinionated default Digital Ocean 'droplet' resource.

## Availability

This module is designed for internal use but if useful can be shared publicly.

## Usage

### Requirements

Somewhere in your project you must have specified an access token for the digital ocean provider like this:

```
# Digital Ocean provider configuration

provider "digitalocean" {
    token = "TOKEN"
}
```

Where `TOKEN` is usually defined using a variable, e.g. `"${var.digital_ocean_token}"`.

### Variables

* `hostname`
    * Label of the droplet in Terraform and Digital Ocean and its hostname.
    * This variable **MUST** be considered a valid hostname string by the Digital Ocean API.
    * This variable **MUST** be specified.
    * Default: None
* `image`
    * Droplet boot image.
    * See [Digital Ocean's API documentation](https://developers.digitalocean.com/#list-all-images) for details.
    * This variable **MUST** be a valid Digital Ocean image (base image, application, user image, etc.) as a string.
    * Default: "ubuntu-14-04-x64"
* `region`
    * Datacentre in which to create droplet.
    * See [Digital Ocean's API documentation](https://developers.digitalocean.com/#list-all-regions) for details.
    * This variable **MUST** be a valid Digital Ocean region as a string.
    * Default: "lon1"
* `size`
    * Size or scale of droplet, measured in terms of RAM with scaled CPU and Disk resources.
    * See [Digital Ocean's API documentation](https://developers.digitalocean.com/#list-all-sizes) for details.
    * This variable **MUST** be a valid Digital Ocean size as a string.
    * Default: "512mb" 
* `private_networking`
    * Whether to enable private networking, if supported by the selected `region`.
    * See [Digital Ocean's API documentation](https://developers.digitalocean.com/#list-all-regions) for details on which regions support this feature.
    * This variable **MUST** be a quoted boolean value (i.e. "true" or "false")
    * Default: "true"   
* `ssh_fingerprint` 
    * The [fingerprint](http://en.wikipedia.org/wiki/Public_key_fingerprint) of the public key that should be granted root access to the droplet. 
    * To generate the fingerprint of a public key `id_rsa.pub` located in `~` use this command: `ssh-keygen -lf ~/.ssh/id_rsa.pub | awk '{print $2}'`.
    * The public key specified **MUST** be listed in the Digital Ocean account being used prior to using Terraform to create droplets using this key.
    * This variable **MUST** be specified.
    * Default: None

### Define a droplet

E.g.

```javascript
# Resources

# 'MACHINE_LABEL' resource

# VM

module "MACHINE_LABEL" {
    source = "github.com/antarctica/terraform-module-digital-ocean-droplet"
    hostname = "MACHINE_LABEL"
    ssh_fingerprint = "${var.ssh_fingerprint}"
}
```

Where: `MACHINE_LABEL` is the name of the droplet (i.e. its hostname) and `${var.ssh_fingerprint}` is a Terraform variable `ssh_fingerprint`.

E.g.

```javascript
# Resources

# 'bslweb-denizens-prod-web2' resource

# VM

module "bslweb-denizens-prod-web2" {
    hostname = "bslweb-denizens-prod-web2"
    ssh_fingerprint = "${var.ssh_fingerprint}"
}
```

### Outputs

* `ip_v4_address_public`
    * The public IP v4 address of the droplet
* `ip_v4_address_private`
    * The private IP v4 address of the droplet

#### Example usage

Assuming you have an instance of this module labeled `calcifer-satin-prod-node1` and you want to expose it's public IP address as an output named `bslweb-denizens-prod-web2-ip-v4-address-public` you would do something like this in your main terraform file.

```javascript
output "bslweb-denizens-prod-web2-ip-v4-address-public" {
    value = "${module.bslweb-denizens-prod-web2.ip_v4_address_public}"
}
```

## Limitations

* [BASWEB-194](https://jira.ceh.ac.uk/browse/BASWEB-194) - It isn't currently possible to specify more than one SSH key
 when creating a Digital Ocean droplet using Terraform. This means only one user can initially login as root and is a bug.

* [Terraform #57](https://github.com/hashicorp/terraform/issues/57) - It isn't currently possible to set Terraform from Environment variables, and therefore a `terraform.tfvars`
file is needed as a stand-in. This is annoying and is a limitation of the software.

## Contributions

This project welcomes contributions, see `CONTRIBUTING` for our general policy.

## Developing

### Committing changes

The [Git flow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) workflow is used to manage development of this package.

Discrete changes should be made within *feature* branches, created from and merged back into *develop* (where small one-line changes may be made directly).

When ready to release a set of features/changes create a *release* branch from *develop*, update documentation as required and merge into *master* with a tagged, [semantic version](http://semver.org/) (e.g. `v1.2.3`).

After releases the *master* branch should be merged with *develop* to restart the process. High impact bugs can be addressed in *hotfix* branches, created from and merged into *master* directly (and then into *develop*).

### Issue tracking

Issues, bugs, improvements, questions, suggestions and other tasks related to this package are managed through the BAS Web & Applications Team Jira project ([BASWEB](https://jira.ceh.ac.uk/browse/BASWEB)).

## License

Copyright 2015 NERC BAS. Licensed under the MIT license, see `LICENSE` for details.
