# Parameter binding

## Running the app on Docker

```
$ sudo docker pull blabla1337/owasp-skf-lab:java-parameter-binding
```

```
$ sudo docker run -ti -p 127.0.0.1:5000:5000 blabla1337/owasp-skf-lab:java-parameter-binding
```

{% hint style="success" %}
Now that the app is running let's go hacking!
{% endhint %}

## Reconnaissance

Mass assignment is a computer vulnerability where an active record pattern in a web application is abused to modify data items that the user should not normally be allowed to access such as password, granted permissions, or administrator status.

Please take note of the following code in the User.java . This line of code will prove critical for exploiting the parameter binding attack.

```java
@PostMapping("/create")
  public String createUser(User user, Model model) { // here is the issue
    authModel.createUser(user);
    model.addAttribute("content", "Your user has been created");
    return "index";
  }
```

To fully understand the attack we need to examine the properties "User" model, which looks like this:

```java
public User(String username, String password, Boolean isAdmin) {
  this.username = username;
  this.password = password;
  this.isAdmin = isAdmin;
}
```

## Exploitation

Now, let's examine the target application and determine the objective.

![](https://raw.githubusercontent.com/blabla1337/skf-labs/master/.gitbook/assets/java/ParameterBinding/1.png)

Let's register a new user

![](https://raw.githubusercontent.com/blabla1337/skf-labs/master/.gitbook/assets/java/ParameterBinding/2.png)
![](https://raw.githubusercontent.com/blabla1337/skf-labs/master/.gitbook/assets/java/ParameterBinding/3.png)

Log in as the new user

![](https://raw.githubusercontent.com/blabla1337/skf-labs/master/.gitbook/assets/java/ParameterBinding/4.png)

Let's register a new user and intercept the request on Burp.

![](https://raw.githubusercontent.com/blabla1337/skf-labs/master/.gitbook/assets/java/ParameterBinding/5.png)

As we saw in this line of code:

```java
public String createUser(User user, Model model)
```

Maybe if we add another parameter in the request this parameter will also pass to our new User.

![](https://raw.githubusercontent.com/blabla1337/skf-labs/master/.gitbook/assets/java/ParameterBinding/6.png)

Now if we login.

![](https://raw.githubusercontent.com/blabla1337/skf-labs/master/.gitbook/assets/java/ParameterBinding/7.png)

Bingo! We have now created a new user with Admin privileges.

## Additional sources

Please refer to the OWASP cheat sheet for a full complete description about parameter binding attacks.

{% embed url="https://cheatsheetseries.owasp.org/cheatsheets/Mass_Assignment_Cheat_Sheet.html" %}

{% embed url="https://en.wikipedia.org/wiki/Mass_assignment_vulnerability" %}
