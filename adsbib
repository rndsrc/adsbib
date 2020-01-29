#!/usr/bin/env bash

# Define patterns
SEDCMD='s/\\citation{\(.*\)}/\1/p'
KEYPAT='^[0-9]\{4\}[A-Za-z][&.0-9A-Za-z]\{12\}[0-9][A-Z]$'

# Check if we have the necessary tools
function ckcmd {
	if [ ! $(type -P "$1") ]; then
		echo "\`$1\` not found; please install it"
		exit 1
	fi
}
ckcmd curl
ckcmd jq

# Simple argument check
if [ $# -ne 1 ]; then
	echo "usage: $0 <doc>.aux"
	exit 0
fi

# Main program
NAME=${1%.aux}
ADSKEY=${ADSKEY:-$(head -n1 ~/.adsbibrc)}

sed -n "${SEDCMD}" "${NAME}.aux" | tr ',' '\n' | sort | uniq > "${NAME}.tmp"
if [ -f ${NAME}.map ]; then
	for key in $(grep -v "${KEYPAT}" "${NAME}.tmp"); do
		grep "^${key}\t" "${NAME}.map" | cut -f 2
	done > "${NAME}.tmp2"
	KEYS=$(grep -h "${KEYPAT}" "${NAME}.tmp" "${NAME}.tmp2" | sort | uniq | sed 's/^\(.*\)$/"\1"/')
else
	KEYS=$(grep "${KEYPAT}" "${NAME}.tmp" | sed 's/^\(.*\)$/"\1"/')
fi
rm -f "${NAME}.tmp" "${NAME}.tmp2"

KEYS=$(echo $KEYS | tr ' ' ',')
curl	-H "Authorization: Bearer:${ADSKEY}"                 \
	-H 'Content-Type: application/json'                  \
	   'https://api.adsabs.harvard.edu/v1/export/bibtex' \
	-X POST                                              \
	-d "{\"bibcode\": [${KEYS}]}"                       |\
jq -r '.export' > "${NAME}.bib"
