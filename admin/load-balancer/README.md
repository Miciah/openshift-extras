Configuring ActiveMQ
--------------------

The ActiveMQ node routing plug-in must be enabled so that it sends routing
updates that the programs in this directory can read.  Install
rubygem-openshift-origin-node-plugin-activemq and see its included README.md for
instructions.

Configuring the Daemon
----------------------

The daemon must be configured to connect to ActiveMQ. Edit
/etc/openshift/load-balancer.conf and set ACTIVEMQ_USER, ACTIVEMQ_PASSWORD,
ACTIVEMQ_HOST, and ACTIVEMQ_TOPIC to the appropriate credentials, address, and
ActiveMQ topic.

Exactly one load-balancer module must be enabled.  A module for F5 BIG-IP LTM
and a module for an load-balancer implementing the LBaaS REST API are included
in this repository.  Edit /etc/openshift/load-balancer.conf to either "f5" or
"lbaas" and then following the appropriate module-specific configuration
described below.

Using F5 BIG-IP LTM
-------------------

The F5 module requires some rubygems that are not shipped with RHEL.  Follow the
following steps to install the required rubygems:

1. Download the f5-icontrol gem from
   https://devcentral.f5.com/internal-forums/aft/1179247

2. Unzip f5-icontrol-10.2.0.2.zip.

3. Run the following command:
   scl enable ruby193 'gem install soap4r-ruby1.9'

4. Run the following command:
   scl enable ruby193 'gem install f5-icontrol-10.2.0.2.gem'

After enabling the F5 module as described in the section on configuring the
daemon, edit /etc/openshift/load-balancer.conf to set the appropriate
values for BIGIP_HOST, BIGIP_USERNAME, and BIGIP_PASSWORD to match your F5
BIG-IP LTM configuration.

F5 BIG-IP must be configured with a virtual server that has been assigned at
least one VIP.  The daemon will automatically create pools and associated HTTP
class profiles, add these profiles to the virtual server, add members to the
pools, delete members from the pools, and delete empty pools and unused profiles
when appropriate.  Your virtual server must be named "ose-vlan." The daemon will
name the pools after applications following the template
"/Common/ose-#{app_name}-#{namespace}" and create HTTP class profiles that
redirect "/#{app_name}" to pools comprising the gears of the named application.
The HTTP class profiles bypass applications' HAProxy instances and instead route
to the gears via the node's port-proxy.

Using LBaaS
-----------

After enabling the LBaaS module as described in the section on configuring the
daemon, edit /etc/openshift/load-balancer.conf to set the appropriate values for
LBAAS_HOST, LBAAS_KEYSTONE_HOST, LBAAS_USERNAME, and LBAAS_PASSWORD to match
your LBaaS configuration.