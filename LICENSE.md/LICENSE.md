"""
Objectif simile le jeu et devine si la piece est pipe ou pas selon les troix regle


def pipe_ou_pas(histoire):


   return p


"""

"""
ordinateur choisit une piece est le programme lui indique selon son historique si la piece est pipe ou pas 
la distrubution uniforme 
"""

import numpy as np



def get_proba_face(piece,pipe_normal):
    """

    :param piece: int
          1 pour pile
          2 pour face
    :param pipe_normal: string
          "normal " signifie  l'evement avoir une piece normal
          "pipée " signifie  l'evement avoir une piece pipée

    :return: la probabilite qu'une piece soit pile ou face selon l'evement piece normal ou pipée
    """
    proba_piece = 0
    proba_pipe = 0

    if (pipe_normal == "normal"):
        if (piece == 1):  # if piece is heads(pile)
            proba_piece = 1 / 2
        elif (piece == 2):  # if piece is tails(face)
            proba_piece = 1 / 2

    if (pipe_normal == "pipee"):
        if (piece == 1):  # if piece is heads
            proba_piece = 1 / 3
        elif (piece == 2):  # if piece is tails
            proba_piece = 2 / 3

    return proba_piece


def get_proba_pipe_normal(pipe_normal="normal"):
    """

    :param pipe_normal: string
          "normal " signifie  l'evement avoir une piece normal
          "pipée " signifie  l'evement avoir une piece pipée
    :return: return le probabilite d'avoir cette evenement
    """
    if (pipe_normal == "normal"):
        return 1 / 3
    if (pipe_normal == "pipee"):
        return 2 / 3


def regle_majorite(history):
    """

    :param history: []
           c'est historique des pieces choisie par ordinaire  sa sera par  exemple  au premièr coup Pile puis Pile Face, etc

    :return: retourne la classe majoritaire
    """
    result = ""
    erreur = 0

    proba_pipe_normal = get_proba_pipe_normal()
    proba_pipe_pipee = get_proba_pipe_normal(pipe_normal = "pipee")

    result_normal = proba_pipe_normal
    result_pipee = proba_pipe_pipee



    if(result_normal > result_pipee):
       result = "normal"
       erreur = result_normal - result_pipee
    elif(result_normal < result_pipee):
        result = "pipée"
        erreur =  result_pipee - result_normal
    return result, erreur


def max_vraisemblance(history):
    """

    :param history: []
           c'est historique des pieces choisie par ordinaire  sa sera par  exemple  au premièr coup Pile puis Pile Face, etc

    :return: retourne la classe majoritaire
    """
    result = ""
    erreur = 0

    proba_pipe_normal = get_proba_pipe_normal()
    proba_pipe_pipee = get_proba_pipe_normal(pipe_normal = "pipee")

    result_normal = proba_pipe_normal
    result_pipee = proba_pipe_pipee

    for choice_piece in history:
        "D'après la loi binomial "
        proba_piece_normal = get_proba_face(choice_piece, "normal")
        proba_piece_pipee = get_proba_face(choice_piece, "pipee")

        result_normal *= proba_piece_normal
        result_pipee *= proba_piece_pipee
    print(result_normal,result_pipee)
    if(result_normal > result_pipee):
       result = "normal"
       erreur = result_pipee
    elif(result_normal < result_pipee):
        result = "pipée"
        erreur =  result_normal
    return result, erreur


def get_all_evement_choiced(history):
    """

    :param history: []
           c'est historique des pieces choisie par ordinaire  sa sera par  exemple  au premièr coup Pile puis Pile Face, etc

    :return: retourne la probabilite d'avoir les piece ou la piece selon history independamment de l'evenement
    """
    result = ""
    erreur = 0

    proba_pipe_normal = get_proba_pipe_normal()
    proba_pipe_pipee = get_proba_pipe_normal(pipe_normal = "pipee")

    result_normal = proba_pipe_normal
    result_pipee = proba_pipe_pipee

    for choice_piece in history:
        "D'après la loi binomial "
        proba_piece_normal = get_proba_face(choice_piece, "normal")
        proba_piece_pipee = get_proba_face(choice_piece, "pipee")

        result_normal *= proba_piece_normal
        result_pipee *= proba_piece_pipee
    result = result_normal + result_pipee

    return result

def regle_baye(history):
    """

     :param history: []
            c'est historique des pieces choisie par ordinaire  sa sera par  exemple  au premièr coup Pile puis Pile Face, etc

     :return: retourne la meilleur probabilite ,sachant les pieces quel est la  probabilite que ces piece soit tranque ou pas , et l'erreur
     """
    result = ""
    erreur = 0

    result_normal = 0

    proba_pipe_normal = get_proba_pipe_normal()
    proba_pipe_pipee = get_proba_pipe_normal(pipe_normal = "pipee")
    result_normal = proba_pipe_normal
    result_pipee = proba_pipe_pipee

    for choice_piece in history:
        "D'après la loi binomial "
        proba_piece_normal = get_proba_face(choice_piece, "normal")
        proba_piece_pipee = get_proba_face(choice_piece, "pipee")
        print(" //",proba_piece_normal," ",proba_piece_pipee)
        result_normal *= proba_piece_normal
        result_pipee *= proba_piece_pipee

    result_bayes_normal =  (result_normal  * proba_pipe_normal) / get_all_evement_choiced(history)
    result_bayes_pipee  =  (result_pipee  * proba_pipe_pipee) / get_all_evement_choiced(history)

    # formule de bayes

    if(result_bayes_normal > result_bayes_pipee):
       result = "normal"
       erreur = result_bayes_pipee
    elif(result_bayes_normal < result_bayes_pipee):
        result = "pipée"
        erreur = result_bayes_normal
    return result, erreur



def game(history = list(), list_result=list(), liste_erreur = list(), nb_game = 5):
    """

    :param history: []
     le choix de l'ordiateur au cour du nombre de foie
    :param list_result: []
    :param liste_erreur: []
    :param nb_game: int :int
    :return:
    """

    if(nb_game == 0):
        return  list_result, liste_erreur

    choix_ordinateur = int(np.random.uniform(1, 3))

    history.append(choix_ordinateur)

    result_maj, erreur_maj = regle_majorite(history)

    result_maxv, erreur_maxv = max_vraisemblance(history)

    result_rbayes, erreur_rbayes = regle_baye(history)


    list_result.append([result_maj,result_maxv,result_rbayes])
    liste_erreur.append([erreur_maj,erreur_maxv,erreur_rbayes])
    print(history)
    print(list_result)
    print(liste_erreur)



    nb_game = nb_game - 1
    game(history, list_result, liste_erreur, nb_game)









res = game(nb_game = 10 )

print(res)
