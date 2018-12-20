#!/bin/bash

function go_to_host {
    ssh -i "${KEY}" "${USER}@${TARGET_HOST}"
}

function scp_from_host {
    source_file=$(echo $1 | cut -f 1 -d':')
    target_file=$(echo $1 | cut -f 2 -d':')
    scp -r -i "${KEY}" "${USER}@${TARGET_HOST}:${source_file}" "${target_file}"
}

function scp_to_host {
    source_file=$(echo $1 | cut -f 1 -d':')
    target_file=$(echo $1 | cut -f 2 -d':')
    scp -i $4 "${source_file}" "$USER@${TARGET_HOST}:${target_file}"
}

CMD=0
INVENTORY="/etc/ansible/hosts"
GROUP=$(echo ${!#} | cut -d"/" -f1)
INDEX=$(echo ${!#} | cut -d"/" -f2)

for param; do
    case $param in
	--inventory )   INVENTORY=$2; shift; shift ;;
	--user )        USER=$2; shift; shift ;;
	--key )         KEY=$2; shift; shift ;;
	--get )         CMD=1; ARG=$2; shift; shift ;;
	--put )         CMD=2; ARG=$2; shift; shift ;;
	-- )            shift; break ;;
	* )             break ;;
    esac
done

[ -z ${KEY} ] && echo "Error: no key provided" && exit 1
[ -z ${USER} ] && echo "Error: no user name provided" && exit 1

TARGET_HOST=$(ansible-inventory --inventory "${INVENTORY}" --list | jq ".$GROUP.hosts[$INDEX]" | tr -d '"')

case ${CMD} in
    0)  go_to_host;;
    1)  scp_from_host ${ARG};;
    2)  scp_to_host ${ARG};;
esac
