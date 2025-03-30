**Log in with JWT**
1. extract username and password from request body:
  ```const { username, password } = req.body```
2. Look for a suer with provided username in database:
  ```const user = await User.findOne({ username }) //returns first user that matches the username or null if not found```
3. Check if password is correct using bcrypt
  ```
  const passwordCorrect = user === null 
  ? false 
  : await bcrypt.compare(password, user.passwordHash)
  ```
4. Return error if user doesn't exist or password is wrong:
  ```
    if (!(user && passwordCorrect)) {
    return res.status(401).json({
      error: 'invalid username or password'
    })
  }
  ```
5. If authentication succeeds, create a JWT token
  ```
    const userForToken = {
    username: user.username,
    id: user._id
  }
  const token = jwt.sign(
    userForToken,
    process.env.SECRET,
    { expiresIn: '30d' }
  )
  ```
  - Creates a token containing the user's username and ID --Never include sensitive Data as it canbe decoded,only include with minimal necessary data (usually just userID and name)
  - Signs it with a secret key from environment variables
  - Sets it to expire in 30 days
6. Finally,send response with:
  ```
    res.status(200).send({
    token,        // The JWT token for future authentication
    username: user.username,
    name: user.name
  })
  ```

**create JWT**
- JWT token strcuture: header.payload.signiture
- when we did ```const token = jwt.sign(userForToken,process.env.SECRET,{ expiresIn: '30d' })```, we created a token with encodedheader, encoded payload(the user obj) and the signature
- The signature is generated using payload, the secret```process.env.SECRET``` and the hashing algorithm
- the header is automatically created, including algarithum and type
- any additional claims like ```exp``` are added to the payload based on options, such as ```{ expiresIn: '30d' }```

**Verify JWT**
- when we call```jwt.verify(token, secret) ```,the signiture is verified by using the SECRET,if valid, it returens the decoded payload as a convenience.
- the decoding part can be done by anyone without the SECRET key,the payload is only encoded(Base64URL), not encrypted.
- only the verificaiton part requireds the secret key. This ensures that while anyone can read the token's contents, only those with the secret key can verify it's authentic and hasn't been modified.

**Why JWT is considered secure**
1. Authenticity: The signature proves that the token was created by your server (using your secret) and not forged by someone else. Only someone with the secret can create a valid signature.
2. Integrity: The signature ensures the payload hasn't been modified. If someone tries to change the payload (like changing the user ID to impersonate another user), the signature won't match anymore.
3. Expiration Verification: jwt.verify() also checks if the token has expired, preventing the use of old tokens.

- The security comes from:
  - You can't create a valid token without knowing the secret
  - You can't modify a token without invalidating the signature
- For truly sensitive data that needs to be kept secret, you wouldn't put it in a JWT payload at all - you'd keep it in your database and use the JWT just for authentication.
- In practical terms, JWT is considered secure for authentication because it prevents impersonation and tampering, not because it keeps data secret.
- It's like driver license allows me to drive, everyone can read it ,but only government can issue it, and no one can modify it!