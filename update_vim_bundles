#!/bin/bash
logfile=/tmp/update_vim_bundles_$$.log
changes=""
showchanges="no"
clean="no"

green='\e[32m'
red='\e[31m'
reset='\e[00m'
bold_on='\033[1m'
bold_off='\033[0m'

function cleanup {
   echo -e "cleaning up ..."
   git prune 
   git gc 
}

while getopts "cvl" OPTION; do
   case $OPTION in
      c) showchanges="console";;
      v) showchanges="editor";;
      l) clean="yes";;
   esac
done

echo -e "log file is $bold_on$logfile$bold_off"
for dir in $(find ~/.vim/bundle/* -maxdepth 0 -type d)
do
   plugin_name=$(echo $dir | cut -f6 -d'/')
   echo -e "checking $bold_on$plugin_name$bold_off"
   cd $dir
   local_id=$(git log --oneline | head -n1 | cut -f1 -d' ')
   git fetch -q &> /dev/null
   diff=$(git diff origin/master)

   if [ -z "$diff" ]; then
      if [ "$clean" == "yes" ]; then
         cleanup
      fi
      continue
   fi

   echo -e "  $green repository updated! $reset"
   echo    "   writing changes to the log file ..."
   changes=$(git log --oneline origin/master $local_id..)
   echo -e "$green$plugin_name$reset" >> $logfile
   echo    "$changes" >> $logfile
   echo " " >> $logfile
   echo "   mergin' changes ..."
   git merge origin/master &> /dev/null
   echo "   merged!"

   if [ "$clean" == "yes" ]; then
      cleanup
   fi
done

if [ -n "$changes" ]; then
   if [ "$showchanges" == "console" ]; then
      echo " "
      cat $logfile
   fi      
   if [ "$showchanges" == "editor" ]; then
      vim $logfile
   fi      
else
   echo -e "$red\bno updates. $reset"
fi
