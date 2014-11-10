#!/usr/bin/python -tt

import subprocess
import click
import logging

@click.command()
@click.option('-c', '--config', required=False, type=click.Path(exists=True), 
    default='/etc/yum.conf',
    help='config file to use (defaults to /etc/yum.conf)')
@click.option('-l', '--log-file', required=False, type=click.Path(),
    default='/var/log/repotool.log',
    help='Log file (defaults to /var/log/repotool.log)')
@click.option('-r', '--repoid', required=True, 
    help='repo id to query')
@click.option('-p', '--path', required=True, type=click.Path(exists=True), 
    help='repo path')
@click.option('-v', '--verbose', is_flag=True, 
    help='verbose output')
def updateRepo(repoid, path, config, log_file, verbose):
    args = [
        "/usr/bin/reposync", "--plugins", "--downloadcomps", 
        "--norepopath", "-c", config, "-r", repoid, "-p", path
    ]
    
    logging.basicConfig(level=logging.INFO, 
                        filename=log_file, # log to this file
                        format='%(asctime)s %(message)s') # include timestamp
    
    # reposync -l -m --norepopath -r centosplus -p /var/repos/centos/7/centosplus/x86_64/
    logging.info("Syncing %s to %s", repoid, path)
    if verbose:
        click.echo("Syncing %s to %s" % (repoid, path))
    
    sync = subprocess.Popen(args, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
    out, err = sync.communicate()
    
    if sync.returncode != 0:
        logging.critical(err)
        click.echo(err)
    else:
        if verbose:
            click.echo("Successfully synced %s" % repoid)
        logging.info("Successfully synced %s", repoid)
    
    # createrepo --update -v /var/repos/centos/7/centosplus/x86_64/
    logging.info("Updating metadata for %s", repoid)
    if verbose:
        click.echo("Updating metadata for %s" % repoid)
    
    crepo = subprocess.Popen(
        ["/usr/bin/createrepo", "--update", path], 
        stdout=subprocess.PIPE, 
        stderr=subprocess.PIPE
    )
    out, err = crepo.communicate()
    
    if crepo.returncode != 0:
        logging.critical(err)
        click.echo(err)
    else:
        if verbose:
            click.echo(out)
            click.echo("Successfully updated metadata for %s" % repoid)
        logging.info("Successfully updated metadata for %s", repoid)

if __name__ == '__main__':
    updateRepo()
