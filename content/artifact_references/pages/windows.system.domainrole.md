---
title: Windows.System.DomainRole
hidden: true
tags: [Client Artifact]
---

This artifact will extract Domain Role per machine.


```yaml
name: Windows.System.DomainRole
author: 'Matt Green - @mgreen27'
description: |
   This artifact will extract Domain Role per machine.

type: CLIENT

parameters:
   - name: HostNameRegex
     description: Regex filter by DNSHostName
     default: .
   - name: DomainRegex
     description: Regex filter by Domain
     default: .
   - name: RoleRegex
     description: Regex filter by Role
     default: .
     
sources:
  - precondition:
      SELECT OS From info() where OS =~ 'windows'

    query: |
        SELECT 
            Domain, 
            DNSHostName, 
            if(condition= DomainRole=0,
                then='Standalone Workstation',
                else=if(condition= DomainRole=1,
                    then='Member Workstation',
                    else=if(condition= DomainRole=2,
                        then='Standalone Server',
                        else=if(condition= DomainRole=3,
                            then='Member Server',
                            else=if(condition= DomainRole=4,
                                then='Backup Domain Controller',
                                else=if(condition= DomainRole=5,
                                    then= 'Primary Domain Controller',
                                    else= 'Unknown' )))))
                ) AS DomainRole
        FROM wmi(query='SELECT * FROM Win32_ComputerSystem',namespace='ROOT/cimv2')
        WHERE 
            DNSHostName =~ HostNameRegex
            AND Domain =~ DomainRegex
            AND DomainRole =~ RoleRegex
```
