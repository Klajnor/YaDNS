# Script Name: UpdateYaDNS
# Created by Klajnor 16.05.2013
# Last update by Klajnor 21.05.2013
# Last update by Klajnor 22.05.2013
# Last update by Klajnor 31.05.2013: Update subdomain record
# Last update by Klajnor 07.02.2014: /ip address remove [ find dynamic=yes invalid=yes ]
# Tested on 6.0rc14 and 6.0-6.9
 
# Set needed variables
 
#Root domain
:local YaDNSdomain "domain.net"
:local YaDNSsubdomain "domain.net"
 
# read http://api.yandex.ru/pdd/doc/api-pdd/reference/api-dns_get_token.xml#api-dns_get_token
:local YaDNStoken "123321"
 
# read http://api.yandex.ru/pdd/doc/api-pdd/reference/api-dns_edit_a_record.xml
:local YaDNSrecordid "123456789"
:local YaDNSTTL "300"
 
:global YaDNSForceUpdateOnce
:global YaDNSPreviousIP
:local YaDNSInterfaceName "InterfaceName"
:local YaDNSDomainRecord
 
# get the current IP address from the interface
 
:if ([:len [/interface find name=$YaDNSInterfaceName]] = 0 ) do={
 :log info "UpdateYaDNS: No interface named $YaDNSInterfaceName , please check configuration."
 :error "UpdateYaDNS: No interface named $YaDNSInterfaceName , please check configuration."
}
 
:if ([:typeof $YaDNSPreviousIP] = "nothing" ) do={ :global YaDNSPreviousIP 0.0.0.0 }
 
/ip address remove [ find dynamic=yes invalid=yes ]
:local YaDNSYaDNSCurrentIPMask [ /ip address get [/ip address find interface=$YaDNSInterfaceName] address ]
 
:local YaDNSCurrentIP [:pick $YaDNSYaDNSCurrentIPMask 0 [:find $YaDNSYaDNSCurrentIPMask "/"]]
 
 
:if ([ :typeof $YaDNSCurrentIP ] = "nothing" ) do= {
 :log info "UpdateDynDNS: No ip address present on $YaDNSInterfaceName, please check."
 :error "UpdateDynDNS: No ip address present on $YaDNSInterfaceName, please check."
}
 
:local YaDNSsrcpath1 ( "nsapi/get_domain_records.xml\?token=" . $YaDNStoken . "&domain=" . $YaDNSdomain )
 
:local YaDNSAPI [:resolve "pddimp.yandex.ru"]
/tool fetch mode=https address="$YaDNSAPI" host="pddimp.yandex.ru" src-path=$YaDNSsrcpath1 dst-path="/YaDNSGetDomainRecord.txt"
 
:local Result1 [/file get YaDNSGetDomainRecord.txt contents]
:local Result2 [:pick $Result1 ([:find $Result1 "id=\"$YaDNSrecordid"]) ([:find $Result1 "id=\"$YaDNSrecordid"]+42) ]
:set YaDNSDomainRecord [:pick $Result2 ([:find $Result2 ">"] + 1) ( [:find $Result2 "<"] ) ]
 
:if (($YaDNSForceUpdateOnce or ($YaDNSCurrentIP != $YaDNSPreviousIP) or ($YaDNSCurrentIP != $YaDNSDomainRecord)) =  true) do={
 
  :log info "UpdateYaDNS: Try Update"
 
  :log info "UpdateYaDNS: YaDNSForceUpdateOnce = $YaDNSForceUpdateOnce"
  :log info "UpdateYaDNS: YaDNSPreviousIP = $YaDNSPreviousIP"
  :log info "UpdateYaDNS: YaDNSCurrentIP = $YaDNSCurrentIP"
  :log info "UpdateYaDNS: YaDNSDomainRecord = $YaDNSDomainRecord"

  :local YaDNSsrcpath2 ( "nsapi/edit_a_record.xml\?token=" . $YaDNStoken . "&domain=" . $YaDNSdomain . "&record_id=" . $YaDNSrecordid . "&ttl=" . $YaDNSTTL . "&content=" . $YaDNSCurrentIP )
 
  if ( $YaDNSdomain != $YaDNSsubdomain ) do={ set YaDNSsrcpath2 ($YaDNSsrcpath2 . "&subdomain=" . YaDNSsubdomain) }

  :local YaDNSAPI [:resolve "pddimp.yandex.ru"]
 
  /tool fetch mode=https address="$YaDNSAPI" host="pddimp.yandex.ru" src-path=$YaDNSsrcpath2 dst-path="/YaDNS.txt"
  :local result [/file get YaDNS.txt contents]
 
  :global YaDNSResult [:pick $result ([:find $result "<error>"]+7) [:find $result "</error>"]]
 
  :if ( $YaDNSResult = "ok" ) do={
    :set YaDNSForceUpdateOnce false
    :set YaDNSPreviousIP $YaDNSCurrentIP
    :log info "UpdateYaDNS: Update Success"
  }
 
  :log info "UpdateYaDNS: Result: $YaDNSResult"
}