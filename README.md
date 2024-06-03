# RPG-Map-Based-Game
A short game where you move around the created "map" towards the finish line. 
# RPG Classes Assignment - Kaden Whiteside
# May 29, 2024

# This code assigns classes to the catagories of items in my TBG
# They can then be called upon by the user, this code can almost be played
# It uses the "map" to allow the user to move from place to place
# They can also call upon items in their "inventory", although it does nothing

# The User/Character Class
class Character:
    """This is the user, the user has an inventory. This class defines that
    along with the starting location of the user."""
    def __init__(self, name, location=(0, 0), health=100):
        """Initialize a new character."""
        self.name = name
        self.inventory = []
        self.location = location
        self.health = health

    # Defines how the charater moves through the map
    def move(self, direction, game_map):
        """Moves the character in the given direction if possible. This
        function also sets a boundry, for example if the user is on the left
        side of the "map", then they cannot go left any further."""
        x, y = self.location
        if direction == "up" and x > 0:
            self.location = (x - 1, y)
        elif direction == "down" and x < len(game_map) - 1:
            self.location = (x + 1, y)
        elif direction == "left" and y > 0:
            self.location = (x, y - 1)
        elif direction == "right" and y < len(game_map[0]) - 1:
            self.location = (x, y + 1)
        else:
            print("You can't move in that direction.")

    # Prints the current user location
    def print_location(self):
        """Prints the current location of the character based on where they
        are on the "map"."""
        print(f"{self.name} is now at location {self.location}.")

    # Prints all info about the user/character
    def print_attributes(self):
        """Prints all attributes of the character in the python shell, this
        includes their name, current location, and their inventory if the
        user asks to see it."""
        print(f"Name: {self.name}")
        print(f"Health: {self.health}")
        print(f"Inventory: {self.inventory}")
        print(f"Location: {self.location}")

    # Prints the damage taken from an enemy
    def take_damage(self, amount):
        """This function reduces the character's health by the damage
        amount."""
        self.health -= amount
        print(f"{self.name} took {amount} damage! Current health: {self.health}")

    # A Consumable will "heal" the user
    def heal(self, amount):
        """This function increases the character's health by the heal
        amount."""
        self.health += amount
        print(f"{self.name} healed {amount} health! Current health: {self.health}")

# Prints the "enemies" for the game
class Enemy(Character):
    """There are two enemies, this class defines who and where they are in
    the game."""
    def __init__(self, name, location):
        super().__init__(name, location)

# Defines the map itself
class Map:
    """This creates the map as well as the coordinates so that the user can
    move throughout the game, my real TBG won't necessarily look like this,
    however this paints a nice picture for it."""
    def __init__(self):
        self.map = [
            ["Tent", "Campfire", "Safe but Dark Trail"],
            ["Campsite", "Dangerous Trail", "Marrr's Lair"],
            ["Forest", "Cliff", "Bear Den"]
        ]
        self.start = (0, 0)
        self.end = (2, 2)
        self.obstacles = {
            "Bear Den": ("A fierce bear blocks the path.", 20),
            "Marrr's Lair": ("A dangerous tiger lurks here.", 30)
        }
        self.enemies = [
            Enemy("Bear", (2, 2)),
            Enemy("Marrr the Tiger", (1, 2))
        ]

    # Prints the map in the shell
    def print_map(self):
        """Print the current game map, it does not change, it is merely a
        reminder for the user as to where each thing is."""
        for row in self.map:
            print(" | ".join(row))
        print()  # Add an extra line for better readability

# Class for the inventory
class Inventory:
    """Contains everything in the user's inventory in seperate lists."""
    def __init__(self):
        self.items = {
            "consumables": {"Bread with honey butter": 10, "Water": 5, "Steak": 20, "Baked potato": 15},
            "utilities": ["Flashlight", "Hiking shoes", "Sneakers"],
            "pants": ["Jeans", "Joggers", "Sweatpants"]
        }

    # Prints the inventory in the shell
    def print_inventory(self):
        """Prints the current inventory, items that have been used by the
        user will have been removed from the inventory."""
        for category, items in self.items.items():
            if isinstance(items, dict):
                print(f"{category.capitalize()}:")
                for item, value in items.items():
                    print(f"  - {item} (heals {value} health)")
            else:
                print(f"{category.capitalize()}:")
                for item in items:
                    print(f"  - {item}")
        print()

    # Determines when an item has been used
    def use_item(self, item_name):
        """Use an item and remove it from the inventory if it exists."""
        if item_name in self.items.get("consumables", {}):
            heal_amount = self.items["consumables"].pop(item_name)
            player.heal(heal_amount)
        else:
            for category, items in self.items.items():
                if item_name in items:
                    items.remove(item_name)
                    print(f"Used {item_name} from {category}.")
                    return
        print(f"{item_name} not found in inventory.")

# The main function
def main():
    """Brings everything together so that the game runs as it should."""
    player = Character("Player")
    game_map = Map()
    inventory = Inventory()

    print("Welcome to my TBG! By Kaden Whiteside.\n")
    game_map.print_map()

    while True:
        action = input("Enter a direction (up, down, left, right), 'inventory' to check items, 'use [item]' to use an item, 'stats' to view character stats, or 'quit' to exit: ")
        if action in ["up", "down", "left", "right"]:
            player.move(action, game_map.map)
            player.print_location()
            x, y = player.location
            current_tile = game_map.map[x][y]
            if current_tile in game_map.obstacles:
                obstacle_desc, damage = game_map.obstacles[current_tile]
                print(obstacle_desc)
                player.take_damage(damage)
            if any(enemy.location == player.location for enemy in game_map.enemies):
                print("You've encountered an enemy!")
            if player.location == game_map.end:
                print("Congratulations! You've reached the end of the map and won the game!")
                break
            if player.health <= 0:
                print("Game over! You've run out of health.")
                break
        elif action == "inventory":
            inventory.print_inventory()
        elif action.startswith("use "):
            item_name = action.split(" ", 1)[1]
            inventory.use_item(item_name, player)
        elif action == "stats":
            player.print_attributes()
        elif action == "quit":
            print("Thanks for playing!")
            break
        else:
            print("Invalid action. Try again.")

if __name__ == "__main__":
    main()

