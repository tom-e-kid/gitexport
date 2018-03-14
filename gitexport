#!/bin/bash
# Create disk image from git archive

# type git || { exit 1; }
# type hdiutil || { exit 1; }

wd=`pwd`
tmpdir="./.__tmp"

dstdir=$wd
basename=`basename $wd`
timestamp=`date '+%Y%m%d%H%M%S'`

branch="master"
format="zip"

function error() {
  if [ -e $tmpdir ]; then
    rm -rf $tmpdir
  fi
  >&2 echo $1
  exit 1
}

while getopts b:d:f: OPT
do
  case $OPT in
    "b" ) branch=$OPTARG ;;
    "d" ) dstdir=$OPTARG ; dstdir=${dstdir%/} ;;
    "f" ) format=$OPTARG ;;
    \? ) error "Usage: `basename $0` [-b branch] [-d dir]";;
  esac
done

if [ -e $tmpdir ]; then
  rm -rf $tmpdir
fi
mkdir $tmpdir || { error "failed to make tmp dir"; }

tmppath=$tmpdir/$basename-$timestamp.$format
dstpath=$dstdir/$basename-$timestamp.$format

if [ $format != "dmg" ]; then
  git archive --v --o=$tmppath --format=$format --prefix=$basename/ $branch || { error "failed to archive"; }
else
  git archive --v --o=$tmpdir/tmp.tar --format=tar --prefix=$basename/ $branch || { error "failed to archive"; }
  tar xvf $tmpdir/tmp.tar -C $tmpdir || { echo "failed to untar"; exit 1; }
  hdiutil create -srcfolder $tmpdir/$basename -volname $basename $tmppath -quiet || { error "failed to create image"; }
fi

mv $tmppath $dstpath
rm -rf $tmpdir

echo $dstpath
