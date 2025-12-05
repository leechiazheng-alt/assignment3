import random
import datetime
import re
import time
import os

WORD_FILE = 'words.txt'

# ANSI 颜色代码
RESET = "\033[0m"
GREEN = "\033[92m"
RED = "\033[91m"
YELLOW = "\033[93m"
CYAN = "\033[96m"
MAGENTA = "\033[95m"


def load_words(filename):
    try:
        with open(filename, "r") as f:
            words = [w.strip().upper() for w in f.readlines() if w.strip()]
        return words
    except FileNotFoundError:
        print(f"{RED}[ERROR]{RESET} {filename} not found!")
        return []


def get_likeness(guess, target):
    return sum(1 for g, t in zip(guess, target) if g == t)


def validate_username(username):
    pattern = r'^[A-Za-z0-9]+$'
    return re.match(pattern, username) is not None


def clear_screen():
    os.system('cls' if os.name == 'nt' else 'clear')


def print_terminal_border():
    print(f"{CYAN}" + "┌" + "─" * 50 + "┐" + RESET)
    print(f"{CYAN}│ CORP_X MAINFRAME SECURITY PROTOCOL{' ' * 15}│{RESET}")
    print(f"{CYAN}" + "└" + "─" * 50 + "┘" + RESET)


def main():
    clear_screen()
    print_terminal_border()

    # ---------------- LOGIN ----------------
    username = input(f"{YELLOW}> LOGIN: {RESET}")
    while not validate_username(username):
        print(f"{RED}> INVALID USERNAME. ALPHANUMERIC ONLY.{RESET}")
        username = input(f"{YELLOW}> LOGIN: {RESET}")

    print(f"{GREEN}> WELCOME, AGENT {username}.{RESET}")
    print(f"{YELLOW}> PASSWORD REQUIRED.{RESET}\n")

    # ---------------- LOAD WORD LIST ----------------
    words = load_words(WORD_FILE)
    if not words:
        return

    options = random.sample(words, random.randint(5, 10))
    password = random.choice(options)
    attempts = 4
    start_time = datetime.datetime.now()

    # ---------------- DEBUG MODE ----------------
    DEBUG = True  # 若要关闭 debug，把 True 改成 False

    if DEBUG:
        print(f"{MAGENTA}[DEBUG] OPTIONS = {options}{RESET}")
        print(f"{GREEN}[DEBUG] PASSWORD = {password}{RESET}\n")

    # Display words with fake hex addresses
    for i, w in enumerate(options):
        print(f"{MAGENTA}0xF{i:02X}    {w}{RESET}")
    print()

    # ---------------- GAME LOOP ----------------
    while attempts > 0:
        elapsed = (datetime.datetime.now() - start_time).seconds
        remaining = max(0, 60 - elapsed)
        print(f"{CYAN}> TIME LEFT: {remaining} sec{RESET}")

        if elapsed > 60:
            print(f"\n{RED}>>> ALARM TRIGGERED! SYSTEM LOCKDOWN!{RESET}")
            return

        try:
            guess = input(f"{YELLOW}> INPUT PASSWORD: {RESET}").upper()
            if guess not in options:
                raise ValueError("WORD NOT IN LIST")
        except ValueError:
            print(f"{RED}> ERROR: INVALID INPUT. TRY AGAIN.{RESET}\n")
            continue

        if guess == password:
            print(f"\n{GREEN}> ACCESS GRANTED.{RESET}")
            return

        likeness = get_likeness(guess, password)
        attempts -= 1
        print(f"{RED}> ACCESS DENIED.{RESET}")
        print(f"{CYAN}> LIKENESS = {likeness}{RESET}")
        print(f"{CYAN}> ATTEMPTS LEFT: {attempts}\n{RESET}")

    print(f"{RED}> SYSTEM LOCKED.{RESET}")
    print(f"{RED}> TERMINAL SHUTDOWN.{RESET}")


if __name__ == "__main__":
    main()
