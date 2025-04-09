# Welcome to your organization's demo respository
Intégration de l'API GitHub avec requests ou PyGithub

Nous allons d'abord explorer comment utiliser l'API GitHub pour récupérer des informations sur les dépôts d'un utilisateur. Tu pourras ensuite intégrer ces informations dans la timeline de ton faux profil.
1. Récupérer les dépôts publics avec requests

Voici comment tu peux récupérer les repos publics d'un utilisateur GitHub en utilisant la bibliothèque requests en Python.

import requests

def get_github_repos(username):
    url = f"https://api.github.com/users/{username}/repos"
    response = requests.get(url)
    if response.status_code == 200:
        return response.json()  # Retourne la liste des repos publics
    else:
        return {"error": response.json()}

# Exemple d'utilisation
username = "octocat"  # Remplace par un nom d'utilisateur GitHub
repos = get_github_repos(username)
for repo in repos:
    print(f"Nom du dépôt: {repo['name']}")
    print(f"Description: {repo['description']}")
    print(f"URL: {repo['html_url']}")
    print("-----")

Cela va te permettre de récupérer les dépôts publics associés à un utilisateur GitHub. Pour chaque dépôt, tu peux afficher des informations comme le nom, la description et l'URL du dépôt.
2. Récupérer les contributions avec PyGithub

Si tu préfères une bibliothèque Python dédiée pour interagir avec l'API GitHub, tu peux utiliser PyGithub. Cela simplifie l'accès aux données de GitHub et permet des interactions plus complexes.

    Installe PyGithub avec la commande suivante :

pip install PyGithub

    Utilise PyGithub pour obtenir les informations des dépôts d'un utilisateur :

from github import Github

# Authentification avec un token (token GitHub personnel, ou laisse vide pour accès public)
g = Github("your_personal_access_token")

def get_user_repos(username):
    user = g.get_user(username)
    repos = user.get_repos()  # Liste tous les dépôts publics
    for repo in repos:
        print(f"Nom du dépôt: {repo.name}")
        print(f"Description: {repo.description}")
        print(f"URL: {repo.html_url}")
        print("-----")

# Exemple d'utilisation
username = "octocat"  # Remplace par le nom d'utilisateur GitHub
get_user_repos(username)

3. Intégrer GitHub dans ton profil

Une fois que tu as récupéré les informations de GitHub, tu peux les intégrer dans la timeline de ton faux profil. Par exemple, pour chaque dépôt GitHub, tu peux créer un post sur le faux profil qui ressemble à une publication de mise à jour de projet. Voici un exemple d'intégration dans une timeline fictive :

from datetime import datetime

# Exemple d'intégration dans la timeline
def generate_github_post(repo):
    post = {
        "author": "Léa (Développeuse)",
        "created_time": datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
        "message": f"J'ai mis à jour mon projet {repo['name']} !\n"
                   f"Description: {repo['description']}\n"
                   f"Venez le découvrir ici: {repo['html_url']}",
        "commentaire": "Merci pour vos retours, je vais améliorer la documentation !"
    }
    return post

# Exemple d'ajout à la timeline pour un dépôt
repos = get_github_repos("octocat")
timeline = []
for repo in repos:
    post = generate_github_post(repo)
    timeline.append(post)

# Afficher la timeline avec les publications
for post in timeline:
    print(f"Post de {post['author']} à {post['created_time']}")
    print(post['message'])
    print(f"Commentaire: {post['commentaire']}")
    print("------")

4. Afficher dans la Timeline Flask

Pour afficher ces publications dans ton interface Flask, il suffit d'ajouter les publications générées dans la timeline. Par exemple, tu peux ajouter ces publications GitHub dans le même format que les posts Facebook.

@app.route("/")
def index():
    # Récupérer le flux de posts via Graph API et les repos GitHub
    posts = get_facebook_feed(PROFILE_ID, ACCESS_TOKEN, SINCE_TIMESTAMP)
    github_repos = get_github_repos("octocat")  # Remplace par le nom d'utilisateur GitHub

    timeline = []

    # Ajouter les posts Facebook à la timeline
    for post in posts:
        post_text = post.get("message", "")
        created_time = post.get("created_time", "")
        auteur = post.get("from", {}).get("name", "Inconnu")
        commentaire = generer_reponse_gpt(FAUX_PROFIL, post_text) if post_text else ""
        timeline.append({
            "auteur": auteur,
            "message": post_text,
            "created_time": created_time,
            "commentaire": commentaire
        })

    # Ajouter les publications GitHub à la timeline
    for repo in github_repos:
        post = generate_github_post(repo)
        timeline.append(post)

    # Tri par date décroissante
    timeline.sort(key=lambda x: x["created_time"], reverse=True)
    return render_template("timeline.html", timeline=timeline, profil=FAUX_PROFIL)

Conclusion

L’intégration de GitHub dans ton projet OSINT/HUMINT permet de rendre le faux profil plus crédible, notamment pour les profils techniques, en ajoutant des publications et des interactions liées à des projets open source.
