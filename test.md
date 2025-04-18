# test

 用于测试git squash操作


[OS] 
name=OS 
baseurl=https://repo.openeuler.org/openEuler-25.03/OS/$basearch/ 
metalink=https://mirrors.openeuler.org/metalink?repo=$releasever/OS&arch=$basearch
metadata_expire=1h 
enabled=1 
gpgcheck=1 
gpgkey=https://repo.openeuler.org/openEuler-25.03/OS/$basearch/RPM-GPG-KEY-openEuler
 
[everything] 
name=everything 
baseurl=https://repo.openeuler.org/openEuler-25.03/everything/$basearch/ 
metalink=https://mirrors.openeuler.org/metalink?repo=$releasever/everything&arch=$basearch
metadata_expire=1h 
enabled=1
gpgcheck=1
gpgkey=http://repo.openeuler.org/openEuler-25.03/everything/$basearch/RPM-GPG-KEY-openEuler

[EPOL]
name=EPOL
baseurl=https://repo.openeuler.org/openEuler-25.03/EPOL/main/$basearch/
metalink=https://mirrors.openeuler.org/metalink?repo=$releasever/EPOL/main&arch=$basearch
metadata_expire=1h
enabled=1
gpgcheck=1
gpgkey=http://repo.openeuler.org/openEuler-25.03/OS/$basearch/RPM-GPG-KEY-openEuler

