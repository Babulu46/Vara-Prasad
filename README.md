import java.io.ByteArrayInputStream;
     import java.io.Console;
     import java.util.List;
     import jdk.jshell.*;
     import jdk.jshell.Snippet.Status;

     class ExampleJShell {
         public static void main(String[] args) {
             Console console = System.console();
             try (JShell js = JShell.create()) {
                 do {
                     System.out.print("Enter some Java code: ");
                     String input = console.readLine();
                     if (input == null) {
                         break;
                     }
                     List<SnippetEvent> events = js.eval(input);
                     for (SnippetEvent e : events) {
                         StringBuilder sb = new StringBuilder();
                         if (e.causeSnippet() == null) {
                             //  We have a snippet creation event
                             switch (e.status()) {
                                 case VALID:
                                     sb.append("Successful ");
                                     break;
                                 case RECOVERABLE_DEFINED:
                                     sb.append("With unresolved references ");
                                     break;
                                 case RECOVERABLE_NOT_DEFINED:
                                     sb.append("Possibly reparable, failed  ");
                                     break;
                                 case REJECTED:
                                     sb.append("Failed ");
                                     break;
                             }
                             if (e.previousStatus() == Status.NONEXISTENT) {
                                 sb.append("addition");
                             } else {
                                 sb.append("modification");
                             }
                             sb.append(" of ");
                             sb.append(e.snippet().source());
                             System.out.println(sb);
                             if (e.value() != null) {
                                 System.out.printf("Value is: %s\n", e.value());
                             }
                             System.out.flush();
                         }
                     }
                 } while (true);
             }
             System.out.println("\nGoodbye");
         }
     }
