# It's going to be legen...wait for it...dary

You get assigned a new project and it will be legendary. In order to get
started, we should first set up a place where we will share our projects code.

As we're stuck with Windows, we have to start first by starting up Git Bash.

Then we can finally get started by writing this:

```bash
mkdir legendary-project
cd legendary-project
git init --bare
```

With shared repository done, we can make a working copy:

```bash
cd ..
git clone legendary-project wip-legendary-project
cd wip-legendary-project
```

We were asked to list exotic animals that look bit like a horse. Sounds bit
stupid, but I guess we can handle it.

Armed with `git show` and `git log`, we can get to work.

So first animal had stripes. Lets write that down in a file.

```bash
echo zebra > animals
```

Next one had long neck. We'll put that into the file also:

```bash
echo giraffe >> animals
```

```bash
git add .
git commit -m "Stupid animals"
```

When looking at the commit message, letting our opinnion show in the commit
message might not be a good idea after all.

Luckily we didn't push the commit yet, so we can still fix the commit message:

```bash
git commit --amend -m "List horse like animals"
```

Now we're ready to tag this as our first release:

```bash
git tag 1.0
```

After checking that content and commit message is OK with `git show`, we'll
push our changes to make boss happy:

```bash
git push
```

# Revenge of the maintenance release

There's message from customer:
> We don't have that kind of animals over here. Can you add pets? 

Let's do just that.

```bash
echo cat >> animals
echo dog >> animals
```

As pets is a major addition, let's tag that as new major release:

```bash
git tag 2.0
```

There's mail from another customer:
> Stupid developers! I can't believe that my favourite animal camel is
> missing!!!1! I want it right now, don't add anything else, or else!

We'll better make quick maintenance release. Hopefully that makes the customer
happy. So we make  a branch awesome_camel. But instead of including anything
from our new and shiny 2.0, we base it on the 1.0 release.

```bash
git checkout -b awesome_camel 1.0
```

What branch we were on again?

```bash
git branch
```

Awesome. On awesome_camel. 

Let's add the sorely missed camel:

```bash
echo camel >> animals
git commit -a -m "Awesome camel"
```

That's maintenance release:
```bash
git tag 1.1
```

Let's push the release, customer is eagerly waiting for it. Let's not mess our
master branch though as we have the new release there waiting.

```bash
git push origin awesome_camel
```

# Red Alert!

Boss is asking that new release should be available for customers yesterday. We
haven't merged maintenance release yet:

```bash
git merge 1.1
```

## Panic! Panic! Abort! Abort!

We got a merge conflict. We can't handle that right now:

```bash
git merge --abort
```

Maybe using the awesome branch name helps:

```bash
git merge awesome_camel
```

There's still merge conflict. I guess we have to just deal with it. 

Let's see what's causing the issue:

```bash
git diff
```

Output looks something like this:

```diff
zebra
giraffe
++<<<<<<< HEAD
+cat
+dog
++=======
+camel
++>>>>>>> 1.1
```

There's what we added to the new release and a camel.

Luckily we are not dealing with code, so we can modify the animals file so it
looks like this:

<pre>
zebra
giraffe
cat
dog
camel
</pre>

Well just commit our fixed file:
```bash
git add animals
git commit
```

Let's see what things look like:
```bash
git log
```

Horrible. There's merge commit and 1.1 release is after 2.0 release.

Let's fix this interactively rebase over whatever we pushed already.

```bash
git rebase -i origin/master
```

Let's switch lines around so that awesome camel is first. Let's save...and
conflict again!

We can use the trick were we remove the conflict markers. Animals file should
look like this after the modification:

<pre>
zebra
giraffe
camel
cat
dog
</pre>

We'll continue the rebase:

```bash
git add animals
git rebase --continue
```

Let's check what happened:
```bash
git log
```

Now releases are in correct order, but 2.0 tag is missing. Or is it?

```bash
git show 2.0
git log 2.0
```

Odd, it's there. But wait, git log shows history before the rebase. We are now
in alternate timeline!

We have to put the 2.0 release back there though. We need to use little bit of
force as 2.0 still exists in alternate history:

```bash
git tag -f 2.0
```

We're ready, so we'll just push the release out:

```bash
git push
```

# Trilogy

Next release is about lizards. I think had something to do with browsers:
```bash
echo firefox >> animals
git commit -a -m "Add a lizard"
git push
```

Wait, it was supposed to be a gecko! We did already push it though, so revert is our best friend.
```bash
git revert HEAD
```

Let's push it before anybody will try to use our faulty commit:
```bash
git push
```
