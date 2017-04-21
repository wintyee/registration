# registration
(for image gallery)
import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;
import java.util.*;
import java.sql.*;
public class RegistrationServlet extends HttpServlet {
private String firstName = "";
private String lastName = "";
private String userName = "";
private String password = "";
/**Process the HTTP Get request*/
public void doGet(HttpServletRequest request, HttpServletResponse response)
throws ServletException,IOException {	
sendRegistrationForm(request, response, false);

}
/**Process the HTTP Post request*/
public void doPost(HttpServletRequest request, HttpServletResponse response)
throws ServletException, IOException {

firstName = request.getParameter("firstName");
lastName = request.getParameter("lastName");
userName = request.getParameter("userName");
password = request.getParameter("password");
boolean error = false;
String message = null;
try {
Class.forName("com.mysql.jdbc.Driver");
Connection con =DriverManager.getConnection("jdbc:mysql://localhost/com.example.wintyee","root","");
System.out.println("got connection");
Statement s = con.createStatement();
String sql = "SELECT UserName FROM Users WHERE userName='" + userName + "'";
ResultSet rs = s.executeQuery(sql);
if (rs.next()) {  //user name already exists
rs.close();
message = "The user name <B>" + userName +
"</B> has been taken. Please select another name.";
error = true;
}
else {
rs.close();
sql = "INSERT INTO Users" +
" (FirstName, LastName, UserName, Password)" +
" VALUES" +
" ('" + firstName + "'," +
" '" + lastName + "'," +
" '" + userName + "'," +
" '" + password + "')";
int i = s.executeUpdate(sql);
if (i==1) {
message = "Successfully added one user.";
}

}
s.close();
con.close();
}
catch (SQLException e) {
message = "Error." + e.toString();
error = true;
}
catch (Exception e) {
message = "Error." + e.toString();
error = true;
}
if (message!=null) {
PrintWriter out = response.getWriter();
out.println("<B>" + message + "</B><BR>");
out.println("<HR><BR>");
}
if (error==true)
sendRegistrationForm(request, response, true);
else
LoginServlet.sendLoginForm(response, false);
}

/**Send the form where the user can type in  the details for a new user */
private void sendRegistrationForm(HttpServletRequest request,
HttpServletResponse response, boolean displayPreviousValues)
throws ServletException, IOException {

response.setContentType("text/html");
PrintWriter out = response.getWriter();
out.println("<HTML>");
out.println("<HEAD>");
out.println("<TITLE>Image gallery</TITLE>");
out.println("</HEAD>");
out.println("<BODY>");
out.println("<CENTER>");
out.println("<BR><H2>Image gallery</H2>");
out.println("<BR><H1>Registration Page</H1>");
out.println("<BR>Please enter the user details.");
out.println("<BR>");
out.println("<BR><FORM METHOD=POST>");
out.println("<TABLE>");
out.println("<TR>");
out.println("<TD>First Name</TD>");
out.print("<TD><INPUT TYPE=TEXT Name=firstName");
if (displayPreviousValues)
out.print(" VALUE=\"" + firstName + "\"");
out.println("></TD>");
out.println("</TR>");
out.println("<TR>");
out.println("<TD>Last Name</TD>");
out.print("<TD><INPUT TYPE=TEXT Name=lastName");
if (displayPreviousValues)
out.print(" VALUE=\"" + lastName + "\"");
out.println("></TD>");
out.println("</TR>");
out.println("<TR>");
out.println("<TD>User Name</TD>");
out.print("<TD><INPUT TYPE=TEXT Name=userName");
if (displayPreviousValues)
out.print(" VALUE=\"" + userName + "\"");
out.println("></TD>");
out.println("</TR>");
out.println("<TR>");
out.println("<TD>Password</TD>");
out.print("<TD><INPUT TYPE=PASSWORD Name=password");
if (displayPreviousValues)
out.print(" VALUE=\"" + password + "\"");
out.println("></TD>");
out.println("</TR>");
out.println("<TR>");
out.println("<TD><INPUT TYPE=RESET></TD>");
out.println("<TD><INPUT TYPE=SUBMIT></TD>");
out.println("</TR>");
out.println("</TABLE>");
out.println("<BR>Already have an account?");
out.println("<BR><a href = /com.example.wintyee/LoginServlet> Log in </a>");
out.println("</FORM>");
out.println("<BR>");
out.println("<BR>");
out.println("</CENTER>");
out.println("</BODY>");
out.println("</HTML>");
}
}

