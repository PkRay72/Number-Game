# Number-Game

import java.util.Random;
import java.util.Scanner;

/**
 * DecodeLabs Industrial Training Kit - Batch 2026
 * Project 1: Number Guessing Game
 *
 * Key Concepts: Random class, loops, conditionals, input handling
 * Features:
 *   - Random number generation (1–100) using java.util.Random
 *   - User input via java.util.Scanner
 *   - High/Low feedback loop
 *   - Attempt limiter (max 7 guesses per round)
 *   - Multiple rounds with Play Again prompt
 *   - Score tracking across rounds
 *   - Input validation (handles non-integer input gracefully)
 */
public class DecodeLabs_Java_P1 {

    // ─── Constants ────────────────────────────────────────────────────────────
    static final int MIN        = 1;
    static final int MAX        = 100;
    static final int MAX_TRIES  = 7;   // Binary-search optimal: ~7 guesses for 1-100

    // ─── Main ────────────────────────────────────────────────────────────────
    public static void main(String[] args) {
        Scanner sc     = new Scanner(System.in);
        Random  random = new Random();

        printBanner();

        int totalRounds = 0;
        int totalWins   = 0;

        // ── Session loop (do-while = always play at least once) ──────────────
        do {
            totalRounds++;
            boolean won = playRound(sc, random, totalRounds);
            if (won) totalWins++;

            System.out.print("\n  Play again? [Y / N] : ");
            sc.nextLine(); // flush buffer after previous nextInt()
            String again = sc.nextLine().trim().toLowerCase();

            if (!again.equals("y")) break;

        } while (true);

        // ── Final score ───────────────────────────────────────────────────────
        printScore(totalRounds, totalWins);
        sc.close();
    }

    // ─── One complete round ──────────────────────────────────────────────────
    static boolean playRound(Scanner sc, Random random, int roundNum) {
        int target   = random.nextInt(MAX) + 1;   // range: 1 – 100 (zero-index shift)
        int attempts = 0;
        boolean won  = false;

        System.out.println("\n  ┌──────────────────────────────────────────┐");
        System.out.printf ("  │  Round %-3d  │  Guess the number (1–100)  │%n", roundNum);
        System.out.printf ("  │             │  You have %d attempts.       │%n", MAX_TRIES);
        System.out.println("  └──────────────────────────────────────────┘");

        // ── Guess loop ────────────────────────────────────────────────────────
        while (attempts < MAX_TRIES) {
            attempts++;
            int remaining = MAX_TRIES - attempts + 1;

            System.out.printf("%n  [Attempt %d/%d — %d left]  Enter guess: ",
                    attempts, MAX_TRIES, remaining);

            int guess = readInt(sc);   // defensive input read

            if (guess < MIN || guess > MAX) {
                System.out.printf("  ⚠  Please enter a number between %d and %d.%n", MIN, MAX);
                attempts--;            // don't penalise for out-of-range
                continue;
            }

            if (guess == target) {
                System.out.printf("%n  ✔  CORRECT! The number was %d. You got it in %d attempt%s!%n",
                        target, attempts, attempts == 1 ? "" : "s");
                won = true;
                break;

            } else if (guess > target) {
                System.out.println("  ↓  Too HIGH — guess lower.");
            } else {
                System.out.println("  ↑  Too LOW  — guess higher.");
            }
        }

        if (!won) {
            System.out.printf("%n  ✘  Out of attempts! The number was %d. Better luck next round!%n",
                    target);
        }

        return won;
    }

    // ─── Defensive integer reader (handles InputMismatchException) ───────────
    static int readInt(Scanner sc) {
        while (true) {
            try {
                int value = sc.nextInt();
                return value;
            } catch (java.util.InputMismatchException e) {
                sc.next(); // discard bad token
                System.out.print("  ⚠  Invalid input. Enter a whole number: ");
            }
        }
    }

    // ─── Banner ───────────────────────────────────────────────────────────────
    static void printBanner() {
        System.out.println();
        System.out.println("  ╔══════════════════════════════════════════════╗");
        System.out.println("  ║      DecodeLabs  ·  Java Project 1           ║");
        System.out.println("  ║          N U M B E R   G A M E               ║");
        System.out.println("  ║  Guess the secret number between 1 and 100   ║");
        System.out.println("  ╚══════════════════════════════════════════════╝");
    }

    // ─── Final score display ─────────────────────────────────────────────────
    static void printScore(int rounds, int wins) {
        int losses = rounds - wins;
        System.out.println("\n  ╔══════════════════════════════╗");
        System.out.println("  ║         FINAL  SCORE         ║");
        System.out.printf ("  ║  Rounds played : %-3d         ║%n", rounds);
        System.out.printf ("  ║  Rounds won    : %-3d         ║%n", wins);
        System.out.printf ("  ║  Rounds lost   : %-3d         ║%n", losses);
        System.out.printf ("  ║  Win rate      : %5.1f %%      ║%n",
                rounds > 0 ? (wins * 100.0 / rounds) : 0.0);
        System.out.println("  ╚══════════════════════════════╝");
        System.out.println("\n  Thanks for playing! — DecodeLabs Batch 2026\n");
    }
}
