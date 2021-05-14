import java.util.*;
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.FileWriter;

public class POS_Viterbi_Tags {
  private static Map<String, Map<String, Double>> PP;
  private static Map<String, Map<String, Double>> Table;
  private static String begin = "start";

  public static void Hash_Map(String sentence) {
    String[] tokens = sentence.split("\\n");
    for (int i = 0; i < tokens.length; i++) {
      String word = tokens[i].split("\\t")[0];
      String tag = tokens[i].split("\\t")[1];
      if (Table.containsKey(tag)) {
        if (Table.get(tag).containsKey(word)) {
          double count = Table.get(tag).get(word) + 1;
          Table.get(tag).put(word, count);
        } else {
          Table.get(tag).put(word, 1.0);
        }
      } else {
        Map<String, Double> counter = new HashMap<String, Double>();
        counter.put(word, 1.0);
        Table.put(tag, counter);
      }

      if (i < tokens.length - 1) {
        if (i == 0) {
          if (PP.get(begin).containsKey(tag)) {
            double count = PP.get(begin).get(tag) + 1;
            PP.get(begin).put(tag, count);
          } else {
            PP.get(begin).put(tag, 1.0);
          }
        }

        if (PP.containsKey(tag)) {
          if (PP.get(tag).containsKey(tokens[i + 1].split("\\t")[1])) {
            double count = PP.get(tag).get(tokens[i + 1].split("\\t")[1]) + 1;
            PP.get(tag).put(tokens[i + 1].split("\\t")[1], count);
          } else {
            PP.get(tag).put(tokens[i + 1].split("\\t")[1], 1.0);
          }
        } else {
          Map<String, Double> tag_count = new HashMap<String, Double>();
          tag_count.put(tokens[i + 1].split("\\t")[1], 1.0);
          PP.put(tag, tag_count);
        }
      }
    }
  }

  public static void Reading(String filename) {
    BufferedReader in = null;
    BufferedWriter out = null;
    String line ="", tagline="", word = "";

    try {
      in = new BufferedReader(new FileReader(filename));
      out = new BufferedWriter(new FileWriter("submission.pos"));

      while ((word = in.readLine()) != null) {
        if (!word.isEmpty()) {
          line = line + word + " ";
        } else {
          tagline = Viterbi(line);
          String[] words = line.split(" ");
          String[] tags = tagline.split(" ");

          for (int i = 0; i < words.length; i++) {
            if (!(Table.get(tags[i]).containsKey(words[i]))) {
              if (words[i].length() >= 3) {
                if (i == 0)
                  tags[i] = Unknown("", words[i], tags[i]);
                else
                  tags[i] = Unknown(words[i - 1], words[i], tags[i]);
              }
            }
          }

          for (int i = 0; i < words.length; i++) {
            out.write(words[i] + "\t" + tags[i] + "\n");
          }
          out.write("\n");
          line = "";
        }
      }
    } catch (Exception e) {
      ;
    }

    try {
      in.close();
      out.close();
    } catch (Exception e) {
      ;
    }
  }

  public static String Viterbi(String input) {
    String outputS = "", LastTag = "";
    double unknown = -10.0, max_prob = -1000;
    String[] words = input.split(" ");
    Stack<String> path = new Stack<String>();
    List<Map<String, String>> backtrace = new ArrayList<Map<String, String>>();

    Set<String> priorT = new HashSet<>();
    Map<String, Double> priorP = new HashMap<String, Double>();
    priorT.add(begin);
    priorP.put(begin, 0.0);

    for (int i = 0; i < words.length; i++) {
      Set<String> nextState = new HashSet<>();
      Map<String, Double> nextProb = new HashMap<String, Double>();
      Map<String, String> backTags = new HashMap<String, String>();
      double probability;

      for (String state : priorT) {
        if (PP.containsKey(state) && !PP.get(state).isEmpty()) {
          for (String st : PP.get(state).keySet()) {
            nextState.add(st);

            if (Table.containsKey(st) && Table.get(st).containsKey(words[i])) {

              probability = priorP.get(state) + PP.get(state).get(st)
                      + Table.get(st).get(words[i]);
            } else {

              probability = priorP.get(state) + PP.get(state).get(st) + unknown;
            }

            if (!nextProb.containsKey(st) || probability > nextProb.get(st)) {
              nextProb.put(st, probability);
              backTags.put(st, state);

              if (backtrace.size() > i)
                backtrace.remove(i);

              backtrace.add(backTags);
            }
          }
        }
      }
      priorP = nextProb;
      priorT = nextState;
    }

    for (String prob : priorP.keySet()) {
      if (max_prob < priorP.get(prob)) {
        max_prob = priorP.get(prob);
        LastTag = prob;
      }
    }
    path.push(LastTag);

    for (int i = words.length - 1; i > 0; i--) {
      path.push(backtrace.get(i).get(path.peek()));
    }

    while (!path.isEmpty()) {
      String token;
      token = path.pop();

      if (outputS == null)
        outputS = (token + " ");
      else
        outputS += (token + " ");
    }

    return outputS;
  }

  public static String Unknown(String s, String words, String tag) {
    int length = words.length();

    if (s.equals("it")) {
      tag = "VBZ";
    } else if (s.equals("would")) {
      tag = "VB";
    } else if (s.equals("be") || words.contains("-")) {
      tag = "JJ";
    }else if (Character.isUpperCase(words.charAt(0))){
      tag = "NNP";
    } else if (words.substring(length - 2, length).equals("ss")) {
      tag = "NN";
    } else if (words.substring(length - 1, length).equals("s")) {
      tag = "NNS";
    } else if (length >= 3 && words.substring(length - 3, length).equals("ble")) {
      tag = "JJ";
    } else if (length >= 3 && words.substring(length - 3, length).equals("ive")) {
      tag = "JJ";
    } else {
      tag = "NN";
    }
    return tag;
  }

  public static void main(String[] args) {
    Table = new HashMap<String, Map<String, Double>>();
    PP = new HashMap<String, Map<String, Double>>();
    PP.put("start", new HashMap<String, Double>());
    BufferedReader br = null;
    String line, sentence = "";
    String training = "/Users/dell/Desktop/WSJ_POS_CORPUS_FOR_STUDENTS/WSJ_02-21.pos";
    String testing = "/Users/dell/Desktop/WSJ_POS_CORPUS_FOR_STUDENTS/WSJ_23.words";

    try {
      br = new BufferedReader(new FileReader(training));
      while ((line = br.readLine()) != null) {
        if (!line.isEmpty()) {
          sentence += line + "\n";
        } else {
          Hash_Map(sentence);
          sentence = "";
        }
      }
    } catch (Exception e) {
      e.printStackTrace();
    } finally {
      try {
        br.close();
      } catch (Exception e) {
        e.printStackTrace();
      }
    }

    for (String a : PP.keySet()) {
      double count = 0;

      for (String b : PP.get(a).keySet())
        count += PP.get(a).get(b);

      for (String b : PP.get(a).keySet()) {
        double logprob = Math.log10(PP.get(a).get(b) / count);
        PP.get(a).put(b, logprob);
      }
    }

    for (String a : Table.keySet()) {
      double count = 0;

      for (String b : Table.get(a).keySet())
        count += Table.get(a).get(b);

      for (String b : Table.get(a).keySet()) {
        double logprob = Math.log10(Table.get(a).get(b) / count);
        Table.get(a).put(b, logprob);
      }
    }
    Reading(testing);
  }
}
