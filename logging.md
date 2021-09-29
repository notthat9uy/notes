# logging updates 
    # Create new file named commands.conf in /etc/rsyslog.d/ with the following content:
    sudo -e /etc/rsyslog.d/commands.conf
    local6.*    /var/log/commands.log
 
    # restart rsyslog
    sudo service rsyslog restart
 
    # Add new log to log rotation
    sudo -e /etc/logrotate.d/rsyslog
    /var/log/mail.warn
    /var/log/mail.err
    [...]
    /var/log/messages
    /var/log/commands.log
 
    # Modify rtib users zshrc and add the following to precmd (added after last fi)
    vim ~/.zshrc
    eval 'RETRN_VAL=$?;logger -p local6.debug "$(whoami) [$$]: $(history | tail -n1 | sed "s/^[ ]*[0-9]\+[ ]*//" ) [$RETRN_VAL]"'
 

    # Add the following to the bottom of the ~/.zshrc file
    # Execute "script" command just once
    smart_script(){
        # if there's no SCRIPT_LOG_FILE exported yet
        if [ -z "$SCRIPT_LOG_FILE" ]; then
            # make folder paths
            logdirparent=~/Terminal_typescripts
            logdirraw=raw/$(date +%F)
            logdir=$logdirparent/$logdirraw
            logfile=$logdir/$(date +%F_%T).$$.rawlog
 
            # if no folder exist - make one
            if [ ! -d $logdir ]; then
                mkdir -p $logdir
            fi
 
            export SCRIPT_LOG_FILE=$logfile
            export SCRIPT_LOG_PARENT_FOLDER=$logdirparent
 
            # quiet output if no args are passed
            if [ ! -z "$1" ]; then
                script -f $logfile
            else
                script -f -q $logfile
            fi
 
            exit
        fi
    }
 
    # Start logging into new file
    alias startnewlog='unset SCRIPT_LOG_FILE && smart_script -v'
 
    # Manually saves current log file: $ savelog logname
    savelog(){
        # make folder path
        manualdir=$SCRIPT_LOG_PARENT_FOLDER/manual
        # if no folder exists - make one
        if [ ! -d $manualdir ]; then
            mkdir -p $manualdir
        fi
        # make log name
        logname=${SCRIPT_LOG_FILE##*/}
        logname=${logname%.*}
        # add user logname if passed as argument
        if [ ! -z $1 ]; then
            logname=$logname'_'$1
        fi
        # make filepaths
        txtfile=$manualdir/$logname'.txt'
        rawfile=$manualdir/$logname'.rawlog'
        # make .rawlog readable and save it to .txt file
        cat $SCRIPT_LOG_FILE | perl -pe 's/\e([^\[\]]|\[.*?[a-zA-Z]|\].*?\a)//g' | col -b > $txtfile
        # copy corresponding .rawfile
        cp $SCRIPT_LOG_FILE $rawfile
        printf 'Saved logs:\n    '$txtfile'\n    '$rawfile'\n'
    }
    smart_script
 

    # Append this to the end of .zshrc
    PROMPT='%{$fg[yellow]%}[%D{%f/%m/%y} %D{%L:%M:%S}] '$PROMPT
