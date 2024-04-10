# cereal
on opening the website, we see the front page looks like - 
![image](https://github.com/Ryuou02/ctf-writeups/assets/133224167/e9c912be-6d72-4ea4-a22b-979178f51398)
here we see that we just need to put ```guest``` as username and ```password``` as password. 

In the source code, we can see that - 
```
<form action="authenticate.php" method="post">
				<label for="username">
					<i class="fas fa-user"></i>
				</label>
				<input type="text" name="username" placeholder="Username" id="username" required>
				<label for="password">
					<i class="fas fa-lock"></i>
				</label>
				<input type="password" name="password" placeholder="Password" id="password" required>
				<input type="submit" value="Login">
			</form>
```
the form leads to the input being sent to ```authenticate.php```
In authenticate.php, we see
```
$conn = new PDO('sqlite:../important.db');
$conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
$query = "select * from users where `username` = :username AND `password` = :password";
$stmt = $conn->prepare($query);
$stmt->bindParam(':username', $username);
$stmt->bindParam(':password', $password);
```
username and password fields are safe from sqli in the login form.
then we also can see - 
```	setcookie($cookie_name, base64_encode(serialize($cookie)), time() + (86400 * 30), "/");```
here a new object of User class is initialised, serialised, base64 encoded and set as cookie. We can see a cookie with the name 'auth'
![image](https://github.com/Ryuou02/ctf-writeups/assets/133224167/895de73b-1969-4d32-9ddf-72764971755a)
this holds the value of the base64 encoded serialized object.

Here we can see a clear <a href="https://medium.com/swlh/exploiting-php-deserialization-56d71f03282a">php deserialisation</a> vulnerability.
