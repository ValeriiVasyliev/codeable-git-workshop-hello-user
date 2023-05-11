# hello-user
A Demo repo for the Git Workshop at Codeable

## Exercise 2
In this exercise, let's learn more git commands and also update the content of this `hello-user` plugin.

### Step 1
Now when we take a deeper look at the content that generated by `@wordpress/create-block`, we figure that the `build` folder is not necessary to be tracked by git. So let's add it to `.gitignore` file.

You can do it by simply open the `.gitignore` file and add `build/` to the end of the file. You may also want to add the `.idea` folder to the `.gitignore` file as well. This is a folder that generated by PHPStorm and it's not necessary to be tracked by git.

Although the setup is simply, the underneath question is: what about those files that already tracked by git? How can we remove them from git?

The answer is: `git rm --cached <file>`. So type the following command in your terminal:
```bash
git rm --cached -r build/
git rm --cached -r .idea/
```
You only need to do the second command if you are using PHPStorm and you previously also commit those files to git.

Run `git status` again and you will see that the `build` folder is no longer tracked by git:
```
On branch excercise-2
Your branch is up to date with 'origin/excercise-2'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	deleted:    build/block.json
	deleted:    build/index.asset.php
	deleted:    build/index.css
	deleted:    build/index.js
	deleted:    build/style-index.css

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   .gitignore
	modified:   README.md
```

The need to use `git rm --cached` is because we don't want to delete the files from our local machine. We only want to remove them from git. As the build files are required for the blocks to render.

### Step 2
Since some changes are being made, we would like to push the changes to our remote repository. So let's commit the changes and push them to the remote repository.
```bash
git add .
git commit -m "Remove build folder from git"
git push
```
However, you may see the following error message:
```
ERROR: Permission to yoren/hello-user.git denied to [YOUR ACCOUNT].
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```
This is because this branch is currently still tracking the `upstream/excercise-2` branch. You can confirm this by running `git branch -vv`.

The following command will fix the issue:
```bash
git push -u origin
```
It does two things: 1. push the changes to the remote repository; 2. set the upstream to `origin/excercise-2`.

You will see the following output:
```
Enumerating objects: 64, done.
Counting objects: 100% (64/64), done.
Delta compression using up to 12 threads
Compressing objects: 100% (42/42), done.
Writing objects: 100% (59/59), 6.79 KiB | 6.79 MiB/s, done.
Total 59 (delta 18), reused 55 (delta 16), pack-reused 0
remote: Resolving deltas: 100% (18/18), completed with 2 local objects.
remote:
remote: Create a pull request for 'solution' on GitHub by visiting:
remote:      https://github.com/1fixdotio/hello-user/pull/new/solution
remote:
To github.com:1fixdotio/hello-user.git
 * [new branch]      excercise-2 -> excercise-2
branch 'excercise-2' set up to track 'origin/excercise-2'.
```

If you do have the write permission to the upstream remote, and you would like to push the changes to the upstream remote, you can run the following command:
```bash
git push -u upstream
```
Doing this will set the upstream to `upstream/excercise-2`. To set the upstream without pushing the changes, you can run the following command:
```bash
git branch -u upstream/excercise-2
```

### Step 3
There is another special file in Git which is `.gitattributes` and it's much less known than `.gitignore`. It's used to tell Git how to handle certain files. The most common use case is to tell Git how to handle line endings.

Create a new file `.gitattributes` and add the following content:
```
# Auto detect text files and perform LF normalization
* text=auto
```
It tells Git to normalize line endings for all text files. This is important because different operating systems have different line endings. For example, Windows uses `\r\n` while Linux uses `\n`. If we don't normalize the line endings, we may see some weird changes in the diff.

### Step 4
The plugin hasn't really been doing what we want, which is to greet the user. So let's update the plugin to do that.

Before we write some code, run:
```shell
npm run start
```
So your changes will be compiled automatically.

Open the `src/edit.js` and `src/save.js` and insert the current user's name to the greeting message. You can get the current user's name by calling `wp.data.select('core').getCurrentUser().name`.

```js
// src/edit.js
export default function Edit() {
	return (
		<p { ...useBlockProps() }>
			{ __( 'Hello', 'hello-user' ) + ` ${wp.data.select("core").getCurrentUser().name }` + '!' }
		</p>
	);
}
```

```js
// src/save.js
export default function save() {
	return (
		<p { ...useBlockProps.save() }>
			{ `Hello ${wp.data.select("core").getCurrentUser().name}!` }
		</p>
	);
}
```

If you're familiar with Gutenberg blocks, you should already know that this is the **ABSOLUTELY WRONG** way to do it. But we are doing it so we can learn more Git concepts along the way!

Let's still commit these changes and discuss how to fix this in our next exercise:
```shell
git add .
git commit -m "Display the username in our block"
git push
```

## Continue the Git journey in exercise 3
Check out the `exercise-3` branch to continue the Git journey:
```shell
git checkout excercise-3
```
