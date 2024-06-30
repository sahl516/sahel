# sahel
import math

class Team:
    def _init_(self, name):
        if not name.strip():
            raise ValueError("Le nom de l'équipe ne peut pas être vide.")
        self.name = name
        self.total_goals_scored = 0
        self.average_scored_goals = 0
        self.total_goals_conceded = 0
        self.average_conceded_goals = 0

    def get_team_stats(self):
        print(f"\nEntrez les statistiques pour l'équipe {self.name}:")
        self.total_goals_scored = validate_input("Total des buts marqués: ")
        self.average_scored_goals = validate_input("Moyenne des buts marqués par match: ")
        self.total_goals_conceded = validate_input("Total des buts encaissés: ")
        self.average_conceded_goals = validate_input("Moyenne des buts encaissés par match: ")

    def display_team_stats(self):
        print(f"\nStatistiques pour l'équipe {self.name}:")
        print(f"Total des buts marqués: {self.total_goals_scored}")
        print(f"Moyenne des buts marqués par match: {self.average_scored_goals}")
        print(f"Total des buts encaissés: {self.total_goals_conceded}")
        print(f"Moyenne des buts encaissés par match: {self.average_conceded_goals}")

def validate_input(prompt, input_type=float, min_value=None, max_value=None):
    while True:
        try:
            user_input = input_type(input(prompt))
            if (min_value is None or user_input >= min_value) and (max_value is None or user_input <= max_value):
                return user_input
            else:
                print("La valeur est hors de la plage autorisée.")
        except ValueError:
            print("Veuillez entrer une valeur valide.")

def get_team_name():
    while True:
        name = input("Entrez le nom de l'équipe: ").strip()
        if name:
            return name
        else:
            print("Le nom de l'équipe ne peut pas être vide.")

def poisson_probability(avg_goals, actual_goals):
    return (math.exp(-avg_goals) * (avg_goals ** actual_goals)) / math.factorial(actual_goals)

def predict_outcome(home_team, away_team):
    max_goals = 5  # Nombre maximum de buts à considérer pour la prédiction
    home_goal_probs = [poisson_probability(home_team.average_scored_goals, i) for i in range(max_goals+1)]
    away_goal_probs = [poisson_probability(away_team.average_scored_goals, i) for i in range(max_goals+1)]

    print(f"\nPrédiction du résultat pour le match entre {home_team.name} et {away_team.name}:\n")

    # Prédiction 1x2 en utilisant les probabilités Poisson
    home_win_prob = sum(home_goal_probs[i] * sum(away_goal_probs[j] for j in range(i)) for i in range(1, max_goals+1))
    draw_prob = sum(home_goal_probs[i] * away_goal_probs[i] for i in range(max_goals+1))
    away_win_prob = sum(away_goal_probs[i] * sum(home_goal_probs[j] for j in range(i)) for i in range(1, max_goals+1))

    print("Prédiction 1x2:")
    if home_win_prob > away_win_prob and home_win_prob > draw_prob:
        print("Victoire de l'équipe 1")
    elif away_win_prob > home_win_prob and away_win_prob > draw_prob:
        print("Victoire de l'équipe 2")
    else:
        print("Match nul")

    print("\nProbabilités:")
    print(f"Victoire de l'équipe 1: {home_win_prob:.2%}")
    print(f"Match nul: {draw_prob:.2%}")
    print(f"Victoire de l'équipe 2: {away_win_prob:.2%}")

    # Prédiction pour les différents scénarios de buts
    total_goals_probs = [sum(home_goal_probs[i] * away_goal_probs[j] for i in range(max_goals+1) for j in range(max_goals+1) if i + j == k) for k in range(2*max_goals+1)]

    print("\nPrédiction pour les différents scénarios de buts:")
    under_1_5_prob = sum(total_goals_probs[:2])
    under_2_5_prob = sum(total_goals_probs[:3])
    under_3_5_prob = sum(total_goals_probs[:4])

    print(f"Prédiction: UNDER 1.5 ({under_1_5_prob:.2%})")
    print(f"Prédiction: UNDER 2.5 ({under_2_5_prob:.2%})")
    print(f"Prédiction: UNDER 3.5 ({under_3_5_prob:.2%})")
    print(f"Prédiction: OVER 1.5 ({1 - under_1_5_prob:.2%})")
    print(f"Prédiction: OVER 2.5 ({1 - under_2_5_prob:.2%})")
    print(f"Prédiction: OVER 3.5 ({1 - under_3_5_prob:.2%})")

    # Prédiction pour 'Les deux équipes marquent'
    both_teams_score_prob = sum(home_goal_probs[i] * away_goal_probs[j] for i in range(1, max_goals+1) for j in range(1, max_goals+1))

    print("\nPrédiction pour 'Les deux équipes marquent':")
    print(f"Prédiction: Oui ({both_teams_score_prob:.2%})")
    print(f"Prédiction: Non ({1 - both_teams_score_prob:.2%})")

    # Prédiction de score exact
    print("\nPrédiction de score exact:")
    for i in range(max_goals+1):
        for j in range(max_goals+1):
            if home_goal_probs[i] * away_goal_probs[j] > 0.01:
                print(f"{i} - {j}: {home_goal_probs[i] * away_goal_probs[j]:.2%}")

def correct_score_prediction(home_team, away_team):
    home_goals = round(home_team.average_scored_goals)
    away_goals = round(away_team.average_scored_goals)
    print(f"Prédiction Score exact basée sur les statistiques: {home_goals} - {away_goals}")

# Exemple d'utilisation
home_team_name = get_team_name()
home_team = Team(home_team_name)

away_team_name = get_team_name()
away_team = Team(away_team_name)

home_team.get_team_stats()
away_team.get_team_stats()

predict_outcome(home_team, away_team)
correct_score_prediction(home_team, away_team) 
equipe 1 france 
equib 2 belguim
demain
