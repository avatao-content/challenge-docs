---
description: >-
  It's really great if you create a challenge that runs locally, but the best
  thing is if you share it with others. To do so, you need to be an Avatao
  challenge creator. Let's see how.
---

# Publish your challenge on Avatao

At Avatao, we believe that there are many smart people around the world whose technical skills are invaluable, and they’re also happy to share their experience with others. That's why we do our best to make the process of content creation and publication as easy as possible. At the same time, we understand that the learning process must be fun and enjoyable. To achieve this, our content has to meet high quality standards.

{% hint style="info" %}
In short, each challenge or tutorial has to pass the automatic and manual review process, before being published. Please find below more details.
{% endhint %}

## Initiate challenge creation on Avatao

1. If we haven't contacted you before, please fill in this form.
2. The content creator should accept the invitation and link their GitHub account with the platform.
3. After accepting the invitation, content creator should see a new menu item called **“Create challenge”** in the sidebar of their **Dashboard**. Please choose a license type (normally, there’s one option) and insert a challenge repository name and a small description.

{% hint style="info" %}
Please choose a _challenge repository name_ that describes what the problem is in the challenge, because this name cannot be changed later on. For example, **don’t call** your repository “SQL injection” because there can be a lot of such challenges.

Try to be more specific such as "_SQL injection in Java via Hibernate_". Later, you’ll see that the name of the repository can be different from the name of the challenge, but we **strongly** **suggest** to have the same to be able to find it easier on GitHub. For example: if the name of your challenge is “_My Fantastic Challenge - 2018_” the name of the repository should be “my-fantastic-challenge-2018”. 
{% endhint %}

## Use Git and GitHub

1. Once the content creator has a GitHub URL they  should clone it via *HTTPS* or *SSH*, if they had already uploaded their pubkey to GitHub.
2. Once the URL is cloned, a staging branch should be created as follow:

   ```bash
    git checkout -b staging
    git push -u origin staging
   ```

3. Since the **master** branch is **write-protected**, **only the staging branch can be used later on**. The main reason is that the English grammar is checked on this branch.
4. Use your preferred challenge template from our [challenge toolbox](https://github.com/avatao-content/challenge-toolbox) and copy all the necessary information \(excluding the `.git` directory\) from there to the Git repository you have just cloned.
5. Commit and push your changes and check whether a **pull request \(PR\)** can be opened to the **master** branch. Note that _Github automatically performs certain tests to see if all metadata is filled properly_. Please always check the warnings on GitHub if something isn’t good. Important: Once the repository is created on GitHub, you’ll have 4 weeks to complete this process, otherwise the challenge repository is automatically merged.
6. When you’reready with a challenge, please open a PR (pull request). GitHub will automatically notify reviewers, when a corresponding PR is ready to be merged. Your team mentor (responsible for guaranteeing the technical quality of your challenge) and our internal reviewers (e.g., English grammar, final quality check) will then check your work. Without their approval, the challenge won’t be merged into the master branch. Note that your challenge building process can be checked at [challenge-builder.avatao.com](https://challenge-builder.avatao.com/).
7. If the challenge passes the reviews and it’s built successfully, the creator should see the challenge on the platform under their name, by choosing from the filter under the Challenges page on the platform.

