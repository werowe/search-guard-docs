Example


Install SearchGuard Plugin in ElasticSearch	2
Generate TLS certificates	2
Load Sample Data to ElasticSearch	3
Kibana Install SearchGuard Plugin	3
Kibana Config	3
FileBeat Configure and Start	4
ElasticSearch Enable SSL	5



 


 




sysctl -w vm.max_map_count=262144


Features
https://floragunn.com/searchguard/


Install SearchGuard Plugin in ElasticSearch
Match correct version.  Show version support matrix.


./elasticsearch-plugin install -b com.floragunn:search-guard-5:5.2.2-12
Generate TLS certificates


https://floragunn.com/tls-certificate-generator/

##Demo Cert Installation

After installing Search Guard, you will find the script here:

<ES dir>/plugins/search-guard-5/tools/install_demo_configuration.sh
Then restart ElasticSearch and then run:

<ES dir>/plugins/search-guard-5/tools/sgadmin_demo.sh

Then elasticsearch.yml will be update to:

 




######## Start Search Guard Demo Configuration ########
searchguard.ssl.transport.keystore_filepath: keystore.jks
searchguard.ssl.transport.truststore_filepath: truststore.jks
searchguard.ssl.transport.enforce_hostname_verification: false



# Whether to enable TLS on the REST layer or not
searchguard.ssl.http.enabled: true
searchguard.ssl.http.keystore_filepath: keystore.jks
searchguard.ssl.http.truststore_filepath: truststore.jks
searchguard.authcz.admin_dn:
  - CN=kirk,OU=client,O=client,L=test, C=de

cluster.name: searchguard_demo
network.host: 0.0.0.0

Print cert:

Issuer: CN=Example Com Inc. Signing CA, OU=Example Com Inc. Signing CA, O=Example Com Inc., DC=example, DC=com

keytool -list  -storepass changeit -keystore keystore.jks -printcert -file /tmp/keystore.crt -alias node-0

keytool -list  -storepass changeit -keystore trusttore.jks -printcert -file /tmp/keystore.crt -alias root-ca-chain

Load Sample Data to ElasticSearch

load_sampledata.sh.

It created two indices: humanresources and finance and populates them with sample data.

Which you can see by telling curl to ignore cert validation and logging with the admin user:

curl --insecure -u admin:admin -k 'https://localhost:9200/_cat/indices?v'


Kibana Install SearchGuard Plugin
Kibana instructions on Git.

bin/kibana-plugin install file:///searchguard-kibana-5.3.2-2.zip

sudo cat /usr/share/kibana/kibana-5.3.2-linux-x86_64/config/kibana.yml


searchguard.basicauth.enabled: true
elasticsearch.username: "kibanaserver"
elasticsearch.password: "kibanaserver"
elasticsearch.url: "https://localhost:9200"
elasticsearch.ssl.verificationMode: none
#elasticsearch.ssl.ca: "/path/to/your/root-ca.pem"



https://github.com/floragunncom/search-guard-docs/blob/master/kibana.md

'''

openssl s_client -connect localhost:9200
 
CONNECTED(00000003)
depth=1 DC = com, DC = example, O = Example Com Inc., OU = Example Com Inc. Signing CA, CN = Example Com Inc. Signing CA
verify error:num=20:unable to get local issuer certificate
verify return:0
---
Certificate chain
 0 s:/C=DE/L=Test/O=Test/OU=SSL/CN=node-0.example.com
   i:/DC=com/DC=example/O=Example Com Inc./OU=Example Com Inc. Signing CA/CN=Example Com Inc. Signing CA
 1 s:/DC=com/DC=example/O=Example Com Inc./OU=Example Com Inc. Signing CA/CN=Example Com Inc. Signing CA
   i:/DC=com/DC=example/O=Example Com Inc./OU=Example Com Inc. Root CA/CN=Example Com Inc. Root CA
'''


 



#searchguard

searchguard.basicauth.enabled: true
elasticsearch.username: "kibanaserver"
elasticsearch.password: "kibanaserver"
elasticsearch.url: "https://localhost:9200"
elasticsearch.ssl.verificationMode: none
#elasticsearch.ssl.ca: "/path/to/your/root-ca.pem"


bin/kibana-plugin install (zip file)

curl --insecure -u admin:admin -k 'https://localhost:9200/_cat/indices?v'


TypeError: "field" is a required parameter
    at FieldAggParamFactory.FieldAggParam.write (http://localhost:5601/bundles/kibana.bundle.js?v=14849:73:9103)
    at http://localhost:5601/bundles/kibana.bundle.js?v=14849:73:5942
    at AggParams.forEach (native)
    at AggParamsFactory.AggParams.write (http://localhost:5601/bundles/kibana.bundle.js?v=14849:73:5900)
    at AggConfigFactory.AggConfig.write (http://localhost:5601/bundles/kibana.bundle.js?v=14849:76:3473)
    at AggConfigFactory.AggConfig.toDsl (http://localhost:5601/bundles/kibana.bundle.js?v=14849:76:4230)
    at http://localhost:5601/bundles/kibana.bundle.js?v=14849:76:25195
    at Array.forEach (native)
    at AggConfigsFactory.AggConfigs.toDsl (http://localhost:5601/bundles/kibana.bundle.js?v=14849:76:24978)
    at http://localhost:5601/bundles/kibana.bundle.js?v=14849:71:890

http://localhost:5601/bundles/kibana.bundle.js?v=14849:73:5942


FileBeat Configure and Start



cat /usr/share/filebeat/filebeat-5.4.0-linux-x86_64/filebeat.yml


 filebeat.prospectors:
- input_type: log
  paths:
    - /tmp/logs/*
output.logstash:
  hosts: ["localhost:5043"]



Start FB:

sudo ./filebeat -e -c filebeat.yml -d "publish"

Logstash Config

bin/logstash -f first-pipeline.conf --config.test_and_exit


input {
    beats {
        port => "5043"
    }
}
# The filter part of this file is commented out to indicate that it is
# optional.
# filter {
#
# }
output {
  elasticsearch {
        ssl_certificate_verification => false
        hosts => [ "https://localhost:9200" ]
    }
}

 


