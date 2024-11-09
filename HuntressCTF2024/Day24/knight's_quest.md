## Knight's Quest
A game where you can only attack and defend, but enemies always mirror your actions. So once you beat the spider and ogre, Gorgmoth shows up with 999999999 stats and will one shot you. So as hackers we can cheese our way to victory!

Let's start by disassembling the binery and finding where Gorgmoth keeps his values, we can try to set them to 0 or something you can kill in one hit.

```
+--------------------------------------------------------------------------------+
| 🛡                                     |                                        |
| -------------------------------------- | -------------------------------------- |
| Player                                 |                                        |
| ❤: 29/99                              | ❤: 0/0                                |
| ⚔: 10                                 | ⚔: 0                                  |
| 🏃: 50                                  | 🏃: 0                                   |
+--------------------------------------------------------------------------------+

Messages:
>You have defeated the Ogre!
> A wild Gorthmog, Destroyer Of Worlds appears!
> You attacked the Gorthmog, Destroyer Of Worlds for 10 damage!
> You have defeated the Gorthmog, Destroyer Of Worlds!
> Congratulations! You have defeated all enemies!
--------------------------------------------------------------------------------
 Your flag submission password is: hmafgAhAalqmQABBOAZtP3OWFegsQDAB
```

```sh
└─$ curl -X POST -H "Content-Type: application/json" -d '{"password":"hmafgAhAalqmQABBOAZtP3OWFegsQDAB"}' http://challenge.ctf.games:PORT/submit

{"flag":"flag{40b5b7e5395ee921cbbc804d4350b9c1}"}
```
