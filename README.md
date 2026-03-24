#####################
# *** ATTENTION *** #
#####################

# Ce fichier **doit être enregistré** dans votre **home** Windows
# (U:\ typiquement si vous êtes à l'IUT, C:\Utilisateur\votre_nom
# si vous êtes sur votre ordinateur) sous le nom « .profile »
#
# Sous Windows 7 et suivant, il peut être difficile de
# donner un tel nom à un fichier : essayer alors de le
# renommer en « .profile. » et son nom final sera
# bien « .profile »
#
# Vérifiez bien le nom du fichier une fois l'enregistrement fait.
# Il faut impérativement que ce soit « .profile » ET dans votre
# home pour que ça marche.

env=~/.ssh/agent.env

agent_load_env ()
{
    test -f "$env" && . "$env" >| /dev/null ;
}

agent_start ()
{
    (umask 077; ssh-agent >| "$env")
    . "$env" >| /dev/null ;
}

agent_load_env

#
# agent_run_state: 0=agent running w/ key; 1=agent w/o key; 2= agent not running
#
agent_run_state=$(ssh-add -l >| /dev/null 2>&1; echo $?)

if [ ! "$SSH_AUTH_SOCK" ] || [ $agent_run_state = 2 ]; then
    agent_start
    ssh-add
elif [ "$SSH_AUTH_SOCK" ] && [ $agent_run_state = 1 ]; then
    ssh-add
fi

unset env
