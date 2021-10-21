# Register

V2: https://www.google.com/recaptcha/admin#list

![](/Illustrations/v2.png)

V3: https://www.google.com/recaptcha/admin#v3signup

![](/Illustrations/v3.png)

# Demo

V2: http://ruslan-website.com/recaptcha/recaptcha-v2.php

V3: http://ruslan-website.com/recaptcha/recaptcha-v3.php

## V2

```
<?php
# client-side
define( 'SITE_KEY' , 'xxx' );

# server-side
define( 'SECRET_KEY' , 'xxx' );
?>

<!doctype html>
<html>
	<head>
		<meta charset="utf-8">
		<title>Recaptcha V 2.0</title>
		<script src='https://www.google.com/recaptcha/api.js'></script>
	</head>

	<body> 

	<?php
	// Checks if form has been submitted
	if ($_SERVER['REQUEST_METHOD'] == 'POST')
	{
		function post_captcha($user_response)
		{
			$fields_string = '';
			$fields = array(
				'secret' => SECRET_KEY,
				'response' => $user_response
			);
			foreach($fields as $key=>$value)
			$fields_string .= $key . '=' . $value . '&';
			$fields_string = rtrim($fields_string, '&');

			$ch = curl_init();
			curl_setopt($ch, CURLOPT_URL, 'https://www.google.com/recaptcha/api/siteverify');
			curl_setopt($ch, CURLOPT_POST, count($fields));
			curl_setopt($ch, CURLOPT_POSTFIELDS, $fields_string);
			curl_setopt($ch, CURLOPT_RETURNTRANSFER, True);

			$result = curl_exec($ch);
			curl_close($ch);

			return json_decode($result);
		}

		// Call the function post_captcha
		$res = post_captcha($_POST['g-recaptcha-response']);

		if ($res->success)
		{
			echo '<br><p>CAPTCHA was completed successfully!</p><br>';
		}
		else
		{
			echo '<p>Please go back and make sure you check the security CAPTCHA box.</p><br>';
		}
	}
	else
	{ ?>
		
		<!-- FORM GOES HERE -->
		<form action="recaptcha-v2.php" method="post">
			<label for="fname">First Name*</label><br>
			<input type="text" name="fname" id="fname" required autofocus><br><br>

			<label for="lname">Last Name*</label><br>
			<input type="text" name="lname" id="lname" required><br><br>

			<label for="email">Email Address*</label><br>
			<input type="email" name="email" id="email" required><br><br>

			<div class="g-recaptcha" data-sitekey="<?php echo SITE_KEY; ?>"></div>
			<br>
			<input type="submit" id="submit" value="Submit">
		</form>

	<?php } ?>

	</body>
</html>
```


## V3

```
<?php
# client-side
define( 'SITE_KEY' , 'xxx' );

# server-side
define( 'SECRET_KEY' , 'xxx' );
?>

<!doctype html>
<html>
	<head>
		<meta charset="utf-8">
		<title>Recaptcha V 3.0</title>
		<script src="https://www.google.com/recaptcha/api.js?render=<?php echo SITE_KEY;?>"></script>
	</head>
	
	<body> 

	<?php
	// Checks if form has been submitted
	if ($_SERVER['REQUEST_METHOD'] == 'POST') 
	{
		function getCaptcha($user_response) 
		{
			$answer = file_get_contents("https://www.google.com/recaptcha/api/siteverify?secret=".SECRET_KEY."&response=".$user_response);
			return json_decode($answer);
		}

		// Call the function post_captcha
		$res = getCaptcha($_POST['g-recaptcha-response']);

		if ($res->success == true && $res->score > 0.5) 
		{
			echo '<br><p>worked out</p><br>';
		} 
		else 
		{
			echo '<br><p>you are a robot</p><br>';
		}
	} 
	else 
	{ ?>
		
		<!-- FORM GOES HERE -->
		<form action="recaptcha-v3.php" method="post">
			<label for="fname">First Name*</label><br>
			<input type="text" name="fname" id="fname" required autofocus><br><br>

			<label for="lname">Last Name*</label><br>
			<input type="text" name="lname" id="lname" required><br><br>

			<label for="email">Email Address*</label><br>
			<input type="email" name="email" id="email" required><br><br>

			<input type="hidden" id="site_key" value="<?php echo SITE_KEY; ?>">
			<input type="hidden" id="g-recaptcha-response" name="g-recaptcha-response">
			<br>
			<input type="submit" id="submit" value="Submit">
		</form>

	<?php } ?>

		<script>
			grecaptcha.ready(function() 
			{
				grecaptcha.execute(document.getElementById('site_key').value, {action: 'homepage'}).then(function(token) 
				{
					document.getElementById( 'g-recaptcha-response' ).value = token;
				});
			});	
		</script>
	</body>
</html>
```

## Tutorials

- https://developers.google.com/recaptcha/docs/versions
- https://recaptcha-demo.appspot.com/
- https://github.com/google/recaptcha
- https://github.com/google/recaptcha/tree/master/examples