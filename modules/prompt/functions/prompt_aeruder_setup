#!/bin/zsh

# A lot from http://www.aperiodic.net/phil/prompt/

PR_FLAGS=()
PR_SAVED_STATUS="0"
autoload -U colors
colors

function pr_aeruder_width {
    local pat
    pat="%{*%}"
    msg=${(%)${(S)${(e)1}//${~pat}}}
    print -n ${#msg}
}

function pr_aeruder_justify {
    local strwidth divider parts targwidth numitems
    local toshift eachshift extrashift premsg

    premsg="$1"
    divider="${2:----}"
    targwidth="${3:-${COLUMNS}}"
    padstring="${4:- }"

    strwidth=$(pr_aeruder_width "$premsg")
    parts="\${(ps:${divider}:)premsg}"
    parts=( ${(e)parts} )
    numitems=${#parts}

    if [[ $numitems == "1" ]]; then
        echo -n "$premsg"
        return
    fi

    (( strwidth = strwidth - (3 * (numitems - 1) ) ))
    (( toshift = (targwidth - strwidth) ))
    (( eachshift = toshift / (numitems - 1) ))
    (( extrashift = toshift - (eachshift * (numitems - 1) ) ))
    if (( eachshift < 0 )) || (( extrashift < 0 )) ; then
        echo -n "$premsg"
        return
    fi

    padder="\${(l.extrashift..${padstring}.)}"
    premsg=${premsg/${divider}/${(e)padder}${divider}}
    padder="\${(l.eachshift..${padstring}.)}"
    premsg=${premsg//${divider}/${(e)padder}}
    echo -n "$premsg"
}

function pr_aeruder_sig_num {
    local val
    val=$PR_SAVED_STATUS
    if ((val == 0)); then
        return 0
    fi

    echo -n '%{${fg_bold[red]}%}'
    if ((val < 128)); then
        echo -n "$val"
    else
        ((val = val-128))
        echo -n `kill -l $val`
    fi
    return 0
}

function pr_aeruder_loadflags {
    local -a flag_strings
    for a in "$PR_FLAGS[@]"; do
        local thisflag="`eval $a`"
        if [[ -z "$thisflag" ]] ; then
            thisflag="-"
        fi
        flag_strings+=( '%{${fg_bold[white]}%}'"$thisflag"'%{${fg_bold[green]}%}' )
    done
    echo "${(e):-${(j/:/)flag_strings}}"
}

function pr_aeruder_git_branch {
  ref=$(git symbolic-ref HEAD 2> /dev/null || git rev-list --abbrev-commit -1 HEAD 2> /dev/null) || return
  echo "%{$fg_no_bold[yellow]%}${ref#refs/heads/}"
}

function pr_aeruder_host {
    if ! [ -z "$SSH_CLIENT" ]; then
        echo "%{${fg_no_bold[green]}%}%n@%m "
    fi
}

function pr_aeruder_pwd {
    local pwd="`print -P %~`"
    local i=0

    if [[ "$pwd" == (#m)(/|"~"[[:IDENT:]]#) ]]; then
        echo "$MATCH"
    else
        pwd=("${(@s:/:)pwd}")
        for (( i = 2; i < ${#pwd}; i++ )); do
            if [[ ${#pwd[i]} > 0 ]]; then
                pwd[i]="${pwd[i][1]}"
            fi
        done
        echo "${(@j:/:M)pwd}"
    fi
}


PR_FLAGS+=(pr_aeruder_sig_num)
PR_FLAGS+=(pr_aeruder_git_branch)

PROMPT='${${PR_SAVED_STATUS::=$?}##*}\
$(pr_aeruder_host)\
%{${fg_bold[magenta]}%}$(pr_aeruder_pwd)%{${fg_bold[white]}%} \
$(pr_aeruder_loadflags) %# %{$reset_color%}'

RPROMPT='\
%{${fg_bold[white]}%}[%{${fg_no_bold[yellow]}%}%D{%H:%M}%{$reset_color%}]'

