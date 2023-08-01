# Tool to get proxys
- This program was made to take the ip and port of a website, and save to a proxy.

# **Installing**

```bash
git clone https://github.com/santosfabin/proxyfetch
cd proxyfetch/
chmod +x proxyfetch.sh
```

---

# **Using proxyfetch**

- There are parameters where it can be seen using `./proxyfetch -h`
- An example to use `./proxyfetch.sh -u 1 -c 10`
- With more parameters `./proxyfetch.sh -u 1 -c 10 -f /home/user/Download -s 5`

---

# Source code

```bash
#!/bin/bash

default_f="/home/fabis/Desktop/teste/pokemons.txt"

url=""
file="$default_f"
socks=""
count=""
proxys=(
"https://www.socks-proxy.net/"
"https://free-proxy-list.net/"
"https://freeproxyupdate.com/socks5-proxy"
"https://freeproxyupdate.com/elite-proxy"
"https://freeproxyupdate.com/anonymous-proxy"
"https://freeproxyupdate.com/"
"https://www.proxysharp.com/proxies/socks5"
"https://list.proxylistplus.com/Socks-List-1"
"https://list.proxylistplus.com/Socks-List-2"
)

function show_help {
	echo "Usage: $0 [OPTIONS]"    
	echo -ne "Syntax for options: ./proxyfetch.sh -u [valueNumber] -c [CountNumber] -s [typeProxy] -f [localFile]\n"
	echo "Example: $0 -u 1 -c 5 -f /home/user/Download -s 5 "
	echo
   	echo "Available options:"
	echo -ne "-u [URL/NUMBER]\t\tSet the URL that contains a proxy table (Required), see the list for more\n"
	echo -ne "-c [COUNT]\t\tSet the number of proxies to add (Required)\n"
	echo -ne "-f [FILE]\t\tSet the file to save the proxies (optional), Default=/etc/proxychains4.conf\n"
	echo -ne "-s [Y|N]\t\tDefine whether to keep the 'SOCKS5' prefix in the file (optional)\n"
	echo -ne "-h, --help\t\tDisplay this help message\n"
	echo -ne "-hl, --help-list\tDisplay a list of available proxy URLs and exit\n"
	exit 1
}

function show_list {
	echo -ne "\033[0;36mAll proxy links saved:\033[0m\n"
	for i in "${!proxys[@]}"; do
		index=$((i + 1))
		echo "[$index] ${proxys[i]}"
	done
	echo -ne "\nTo use put in place of URL\n"
	echo -ne "Examples\n"
	echo -ne "\t./proxyfetch.sh -u 5 -c 1\n"

}

function write_url {
	if echo "$url" | grep -qE '^[0-9]+$'; then
		url="${proxys[url-1]}"
	fi
	urlF=$(echo "$url" | sed -e 's#https\?://\([^/]*\)/.*#\1#')
        
}

function format_socks {
	if ! [[ -z $socks ]]; then
		socks="socks$socks "
	else
		socks="socks5 "
	fi
}

function go {
	clear
	wget -q -O deletme $url
	cat deletme | grep -o '<td>[^<]*</td>' | sed 's/<[^>]*>//g' | grep -v '[[:alpha:]]' | paste -d ' ' - - | grep '\.' | awk '{if (length($1) >= 1 && length($1) <= 6) print $2, $1; else print $1, $2}' | grep -E '^(\S+\s+\S+)$' | head -n "$count" | sed "s/^/$socks/" | sed 's/:/ /g' | awk '{for (i=1; i<=2; i++) printf("%s%s", $i, (i==2) ? RS : OFS)}'>> $file
	
	
	resultado=$(cat deletme | grep -o '<td>[^<]*</td>' | sed 's/<[^>]*>//g' | grep -v '[[:alpha:]]' | paste -d ' ' - - | grep '\.' | awk '{if (length($1) >= 1 && length($1) <= 6) print $2, $1; else print $1, $2}' | grep -E '^(\S+\s+\S+)$' | head -n "$count" | sed "s/^/$socks/" | sed 's/:/ /g' | awk '{for (i=1; i<=2; i++) printf("%s%s", $i, (i==2) ? RS : OFS)}')

	if [ -z "$resultado" ]; then
		echo -ne "\033[0;31mAttention: No output value\033[0m\n"
	else
		echo -ne "\033[0;32mAttention: Output value checked\033[0m\n"
	fi
		
	echo "Url: $url"
	echo "Formatted Url: $urlF"
	echo "Added proxies : $count"
	echo -ne "\033[0;33mFile saved in: $file\033[0m\n"
	if [ -z $socks ]; then
		echo -ne "\033[0;33mSocks format: Not placed\033[0m\n"
	else
		echo "Socks format: $socks"
	fi
	echo
	
	rm deletme
	
}
function error {
	echo "Error"
}

if [ "$#" -lt 1 ]; then
	show_help
	exit 1
fi

while [ "$#" -gt 0 ]; do
	if [ "$1" = "-u" ]; then
		shift
		url="$1"
		write_url
	elif [ "$1" = "-f" ]; then
		shift
		file="$1"
	
	elif [ "$1" = "-s" ]; then
		shift
		socks="$1"
		format_socks
		
	elif [ "$1" = "-c" ]; then
		shift
		count="$1"
		
	elif [ "$1" = "--help-list" ] || [ "$1" = "-hl" ]; then
		show_list
		exit 1
		
	elif [ "$1" = "--help" ] || [ "$1" = "-h" ]; then
		show_help
		exit 1
		
	else
	        echo "Parameters not found: $1"
        	show_help
        exit 1
	fi
    shift
done

if [ -z "$url" ] || [ -z "$count" ] ; then
    echo "The -u and -c parameters are required."
    show_help
    exit 1
fi

go
```
