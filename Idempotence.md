ansible all -m package -a "name=tree state=present"
debian | SUCCESS => {
    "cache_update_time": 1704860215,
    "cache_updated": false,
    "changed": false
}
suse | SUCCESS => {
    "changed": false,
    "name": [
        "tree"
    ],
    "rc": 0,
    "state": "present",
    "update_cache": false
}
rocky | SUCCESS => {
    "changed": false,
    "msg": "Nothing to do",
    "rc": 0,
    "results": []
}

ansible all -m package -a "name=git state=present"
debian | SUCCESS => {
    "cache_update_time": 1704860215,
    "cache_updated": false,
    "changed": false
}
suse | SUCCESS => {
    "changed": false,
    "name": [
        "git"
    ],
    "rc": 0,
    "state": "present",
    "update_cache": false
}
rocky | SUCCESS => {
    "changed": false,
    "msg": "Nothing to do",
    "rc": 0,
    "results": []
}

ansible all -m package -a "name=nmap state=present"
suse | SUCCESS => {
    "changed": false,
    "name": [
        "nmap"
    ],
    "rc": 0,
    "state": "present",
    "update_cache": false
}
debian | SUCCESS => {
    "cache_update_time": 1704860215,
    "cache_updated": false,
    "changed": false
}
rocky | SUCCESS => {
    "changed": false,
    "msg": "Nothing to do",
    "rc": 0,
    "results": []
}
