package exercises.ex4hangman;

import java.io.IOException;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.List;
import java.util.Random;
import java.util.Scanner;

import static java.lang.System.*;

/*
 *  The very well known Hangman game (in a text version)
 *  This is the main program i.e. game logic and the text based IO.
 *
 *  This illustrated the concept of an "OO-model" (i.e. many connected
 *  objects from different classes)
 *
 *  This also demonstrates some file handling (read from disc)
 *
 *  Run this to run the game
 *
 */
public class HangMan {

    public static void main(String[] args) {
        new HangMan().program();
    }

    private final Scanner sc = new Scanner(in);
    private final Random rand = new Random();
    private final String NL = System.lineSeparator();  // System independent new Line

    // Parts of the hanging man (NL is the new line character, don't need to understand)
    private final String[] parts = {
            "---|" + NL,
            "   |" + NL,
            "   O" + NL,
            "  /",
            "|",
            "\\" + NL,
            "  /",
            " \\" + NL,
    };

    private int nGuess = 0;
    private Result result;

    private void program() {

        String theWord;
        try {
            theWord = getWordFromFile();
        }catch (IOException ex){
             out.println("exception" + ex.getMessage());
             return;   // End program if not possible to read the words
        }

        // Create the OO model
        // TODO
        Secret secret = new Secret(theWord);


        /*out.println(secret.getAnswer());
        out.println(secret.getMask());
        secret.updateMask('e');
        out.println(secret.getMask());
        */

        Man man = new Man(8);


        // Start game
        // TODO
        while (true) {
            char ch = guessCharacter();
            if (secret.containsLetter(ch)){
                secret.updateMask(ch);
                if (secret.foundAnswer()){
                    result = Result.WIN;
                    //TODO end
                    break;

                }

            }else {
                    man.hurt();
                    if (man.isDead()){
                        result = Result.LOOSE;
                        //TODO end
                        break;
                    }
            }
            nGuess++;
            plotMan(parts.length - man.getHealth());
            out.println();
            plotMask(secret.getMask());
        }





        // TODO Finally
        if (result == Result.WIN) {
            out.println("You made it. You needed " + nGuess + " tries");
        } else {
            out.println("Sorry you lost. Word(s) was: " + secret.getAnswer() );
        }

    }

    // ---- Game IO don't belong to model classes, put it here ---------------

    // Some file IO for the word list
    private String getWordFromFile() throws IOException {
        Path path = Paths.get("src/exercises/ex4hangman/words.txt");
        List <String> words = FileService.readFile(path);
        String theWord = words.get(rand.nextInt(words.size()));
        return theWord;
    }


    private char guessCharacter() {
        String input;
        while (true) {
            out.print("Enter a char > ");
            input = sc.nextLine();
            if (input.length() == 1 && Character.isLetter(input.charAt(0))) {
                break;
            }
        }
        return input.charAt(0);
    }

    private void plotMask(String mask) {
        for (char ch : mask.toCharArray()) {
            out.print(ch + " ");
        }
        out.println();
    }

    private void plotMan(int nParts) {
        for (int i = 0; i < nParts; i++) {
            out.print(parts[i]);
        }
    }
}
