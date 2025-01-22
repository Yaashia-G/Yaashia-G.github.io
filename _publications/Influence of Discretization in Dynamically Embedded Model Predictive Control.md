---
layout: page
title: Influence of Discretization in Dynamically Embedded Model Predictive Control
description: A counter intuitive effect of discretisation time step in Dynamically Embedded MPC
img: assets/img/system.jpg
importance: 3
category: work
related_publications: true
math: true
---
_Access the paper  <a href="https://www.sciencedirect.com/science/article/pii/S2405896323016956" target="_blank">here </a>. Accepted to IFAC  World Congress 2023_

As is known, Model Predictive Control is a popular control scheme for many complex systems. However, implementing it to continuous systems, $$ \dot x = f(x,u)$$ is almost impossible if we choose to solve a Continuous Time Optimal Control Problem (CT-OCP). So, a more practical method is to solve a Discretised Optimal Control Problem (DT-OCP). One of the ways to find the optimal control law is to solve the DT-OCP using a continuous solver. The result, hereafter
referred to as Dynamically Embedded MPC (DE-MPC), is
a continuous-time MPC scheme that tracks the solution to
the OCP with a bounded error, (cite). In this paper, we analyse the effect of discretisation on DE-MPC. Intuitively having more acccurate discretisation (smaller $$t_d$$) would lead to more stable control law, however that is not true.  

## Introduction
The DE-MPC scheme is when for a continuous time system, $$ \dot x = f(x,u)$$, we solve the discretised OCP, (cite) Eq. 8, using a continuous solver, $$\dot z = \mathcal{T}(z,x)$$. The optimal control is based on the idea that the solution of the optimal control problem can be embedded in the
internal states of a dynamic control law running in parallel to the system. 

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/system.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
 The DE-MPC architecture. By Input-State Stability theorem, for small enough errors, this interconnection of the 3 subsystems is stable.
</div>
For a fixed prediction horizon $$T$$, intuitively, it would make sense that having a smaller discretisation time step $$t_d$$, would reduce the discretisation error, making the control law _more accurate_, however, this does not hold true. While decreasing $$t_d$$, the error between the
continuous-time optimal control policy and its discrete
approximation tends to zero. More importantly,
the closed-loop response becomes slower as $$t_d$$
increases. This behavior is likely due to the fact that the
discretisation becomes increasingly unsta-
ble as $$t_d$$ increases, thereby causing the discretized control to be overdamped compared to the continuous-
time solution. 

The solver on the other hand, assuming a fixed parameter $$x$$, converges faster for an increasing $$t_d$$. This can be explained by noting
that higher values of $$t_d$$ entail lower $$N = T /td$$, thereby
meaning that the optimization problem has fewer variables
and is therefore easier to solve. 

The combination then results in higher values of
td simultaneously slowing down the dynamics of the closed-
loop system and speed up the convergence rate of the
solver. This combined effect makes it easier for the DE-
MPC to track the solution of the discretized OCP as the
discretization step increases. 

## Experiments and Results
Numerical Experiments that illustrate the interesting behaviour of discretisation time step variation were conducted. On a point mass system and a Linearised Lane change and a Nonlinearised Lane change problem. The Linearised Lane change example is shown below.





### Linearised Lane Change
The details of the Point mass system with disturbance can be found in {% cite HMPC %}. We compare the performance of two MPC schemes and a HMPC scheme.
- MPC1: ts = td = 0.02s;
- HMPC: ts = 0.02s and td = 0.4s;
- MPC2: ts = td = 0.4s.
 
Share


You said:
how to edit the size of the jpeg in github pages
ChatGPT said:
ChatGPT
Editing the size of a JPEG in GitHub Pages involves modifying the HTML or CSS of your GitHub Pages site. Below are steps to achieve this:

1. Resize with HTML:
You can control the image size directly in your HTML by specifying the width and height attributes in the <img> tag.

html
Copy
Edit
<img src="path-to-your-image.jpg" alt="Image description" width="300" height="200">
2. Resize with CSS:
If you prefer to keep the HTML cleaner, you can use CSS to control the size.

Inline CSS:
html
Copy
Edit
<img src="path-to-your-image.jpg" alt="Image description" style="width: 300px; height: auto;">
External or Internal CSS:
Add a class or ID to your image in the HTML:

html
Copy
Edit
<img src="path-to-your-image.jpg" alt="Image description" class="image-class">
Then, define the styles in your CSS file or inside a <style> tag:

css
Copy
Edit
.image-class {
    width: 300px;
    height: auto; /* Maintains aspect ratio */
}
3. Dynamic Resizing with Flexbox/Grid:
If your image is part of a layout, you can use flexbox or grid CSS rules to make the image responsive.

css
Copy
Edit
.image-container img {
    max-width: 100%;
    height: auto;
}
html
Copy
Edit
<div class="image-container">
    <img src="path-to-your-image.jpg" alt="Image description">
</div>
4. Responsiveness for Mobile:
To make images responsive, use a percentage or max-width:

css
Copy
Edit
img {
    max-width: 100%;
    height: auto;
}
5. Push Changes to GitHub Pages:
Once you've edited your HTML or CSS:

Commit your changes:
bash
Copy
Edit
git add .
git commit -m "Updated image size"
Push to your GitHub repository:
bash
Copy
Edit
git push
Your site will automatically update with the resized image after the changes are deployed.

Let me know if you need further help!







You said:
---
layout: about
title: about
permalink: /
subtitle: <a href='#'>Affiliations</a>. Address. Contacts. Motto. Etc.

profile:
  align: right
  image: prof_pic.jpg
  image_circular: false # crops the image to make it circular
  more_info: >
    <p>555 your office number</p>
    <p>123 your address street</p>
    <p>Your City, State 12345</p>

news: true # includes a list of news items
selected_papers: true # includes a list of papers marked as "selected={true}"
social: true # includes social icons at the bottom of the page
---

Write your biography here. Tell the world about yourself. Link to your favorite [subreddit](http://reddit.com). You can put a picture in, too. The code is already in, just name your picture prof_pic.jpg and put it in the img/ folder.

Put your address / P.O. box / other info right below your picture. You can also disable any of these elements by editing profile property of the YAML header of your _pages/about.md. Edit _bibliography/papers.bib and Jekyll will render your [publications page](/al-folio/publications/) automatically.

Link to your social media connections, too. This theme is set up to use [Font Awesome icons](https://fontawesome.com/) and [Academicons](https://jpswalsh.github.io/academicons/), like the ones below. Add your Facebook, Twitter, LinkedIn, Google Scholar, or just disable all of them.

crop the pic within this code
ChatGPT said:
ChatGPT
To crop the picture within this code, you can modify the CSS for your prof_pic.jpg to include cropping logic. Unfortunately, YAML headers themselves don't support direct image manipulation, so you'll need to ensure the HTML or CSS in your theme applies the desired cropping.

Here’s how to implement it:

1. Enable CSS Cropping for the Image
Edit your CSS file or add a <style> tag in your layout/theme to include a cropping rule.

CSS for Cropping (Square Crop):
css
Copy
Edit
.profile-image {
    width: 200px; /* Set width of the image */
    height: 200px; /* Set height of the crop area */
    object-fit: cover; /* Ensures the image is cropped */
    border-radius: 0; /* Make sure it's not circular unless needed */
}
2. Link the Style to the Image
Modify the YAML header's image property to include a class:

yaml
Copy
Edit
profile:
  align: right
  image: prof_pic.jpg
  image_circular: false
  image_class: profile-image
  more_info: >
    <p>555 your office number</p>
    <p>123 your address street</p>
    <p>Your City, State 12345</p>
Update your template to apply the class dynamically if it isn't already set up. For example, in your _layouts or theme file:

html
Copy
Edit
<img 
    src="{{ page.profile.image | relative_url }}" 
    alt="Profile Picture" 
    class="{{ page.profile.image_class }}">
3. Optional: Circular Crop
If you want to make the image circular, ensure the CSS includes border-radius: 50%;:

css
Copy
Edit
.profile-image-circular {
    width: 200px;
    height: 200px;
    object-fit: cover;
    border-radius: 50%;
}
Update your YAML to switch to the circular class when image_circular: true:

yaml
Copy
Edit
profile:
  align: right
  image: prof_pic.jpg
  image_circular: true
  image_class: profile-image-circular
  more_info: >
    <p>555 your office number</p>
    <p>123 your address street</p>
    <p>Your City, State 12345</p>
4. Push the Changes
After editing the CSS or layout:

Commit your changes:
bash
Copy
Edit
git add .
git commit -m "Added image cropping logic"
Push to GitHub:
bash
Copy
Edit
git push
The cropped image should now appear as desired on your GitHub Pages site. Let me know if you need further clarification!







You said:
is this change in the code that i gave you?
ChatGPT said:
ChatGPT
The change I provided requires edits outside the YAML front matter of the code you gave me because YAML cannot directly apply CSS for cropping. Here's how the suggested solution integrates with your provided code:

Step-by-Step Explanation for Your Code
1. Add CSS for Image Cropping
You need to add CSS in your site's stylesheet (e.g., assets/css/style.css) to enable the cropping:

css
Copy
Edit
.profile-image {
    width: 200px; /* Desired cropped width */
    height: 200px; /* Desired cropped height */
    object-fit: cover; /* Ensures the image fills the crop area */
    border-radius: 0; /* Change to 50% for circular images */
}
2. Link the CSS to Your YAML
You can modify the image_circular property in your YAML header to toggle circular cropping. Then, add a corresponding class for the image dynamically in your layout files.

Your YAML (No Direct Change):
yaml
Copy
Edit
profile:
  align: right
  image: prof_pic.jpg
  image_circular: false # Change to true for circular crop
  more_info: >
    <p>555 your office number</p>
    <p>123 your address street</p>
    <p>Your City, State 12345</p>
3. Update Your Layout Template
In your _layouts or wherever the profile image is rendered, use logic to apply the cropping style based on image_circular.

Example in HTML Template:
html
Copy
Edit
<img 
    src="{{ page.profile.image | relative_url }}" 
    alt="Profile Picture" 
    class="profile-image{% if page.profile.image_circular %} profile-image-circular{% endif %}">
profile-image applies the cropping rules.
profile-image-circular makes it circular if image_circular: true.
4. Test and Push
After these changes:

Add and commit the changes:
bash
Copy
Edit
git add .
git commit -m "Added cropping logic for profile image"
Push the changes to GitHub:
bash
Copy
Edit
git push
What Was Changed:
CSS: Added styles for cropping.
HTML Template: Dynamically applied CSS classes based on image_circular.
YAML: No direct change; the provided configuration works as-is.
Let me know if you'd like me to guide you on a specific section!







You said:
for github al folio my pages run and deployment is not deploying
ChatGPT said:
ChatGPT
If your GitHub Pages site using the Al-Folio theme is not deploying, here’s a checklist and troubleshooting guide:

1. Verify GitHub Pages Settings
Go to your repository’s Settings > Pages.
Ensure the Source is set to the correct branch (main or gh-pages) and the correct directory (/root or /docs).
Click Save if changes were made.
2. Check Deployment Status
Go to the Actions tab in your repository.
Look for the deployment workflow:
If it failed, click on it to view logs for specific errors.
If it didn’t run, ensure GitHub Actions are enabled.
3. Ensure GitHub Actions is Enabled
Go to Settings > Actions > General.
Ensure Actions permissions are enabled for your repository.
Confirm the workflow file exists: .github/workflows/pages.yml (or equivalent).
4. Verify Workflow Configuration
For Al-Folio, the deployment workflow file (usually .github/workflows/pages.yml) is essential. Ensure it’s correctly configured. Below is a standard configuration:

yaml
Copy
Edit
name: Deploy Jekyll site to GitHub Pages

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Setup Ruby
      uses: ruby/setup-ruby@v1
      with:
        ruby-version: 3.0 # Match Ruby version with Al-Folio requirements

    - name: Install dependencies
      run: bundle install

    - name: Build the site
      run: bundle exec jekyll build

    - name: Deploy to GitHub Pages
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./_site
5. Check for Jekyll Build Errors
Clone the repository locally:
bash
Copy
Edit
git clone https://github.com/your-username/your-repo.git
cd your-repo
Run Jekyll locally to check for errors:
bash
Copy
Edit
bundle install
bundle exec jekyll serve
Fix any issues (e.g., missing dependencies or syntax errors) and commit the changes.
6. Confirm Ruby and Gem Dependencies
Ensure the Gemfile in the repository includes all required dependencies for Al-Folio:
ruby
Copy
Edit
source "https://rubygems.org"

gem "jekyll", "~> 4.3"
gem "minima", "~> 2.5"
gem "jekyll-feed", "~> 0.15"
gem "jekyll-seo-tag", "~> 2.7"
# Other plugins used by Al-Folio
Run the following to ensure dependencies are installed:

bash
Copy
Edit
bundle install
7. Check _config.yml
Ensure the _config.yml file has no syntax errors and contains the correct settings, such as:

yaml
Copy
Edit
url: "https://your-username.github.io"
baseurl: "" # For user/organization pages
8. Verify _site Directory
Ensure that _site (the Jekyll build output directory) is generated during deployment. The workflow deploys the contents of _site to GitHub Pages.
9. Push Changes
After fixing errors, push your changes:

bash
Copy
Edit
git add .
git commit -m "Fix deployment issues"
git push
10. Clear GitHub Pages Cache
If deployment still fails, you can clear the GitHub Pages cache:

Go to Settings > Pages.
Click Clear cache.
Common Errors:
Ruby Version Mismatch: Ensure the ruby-version matches the theme requirements.
Jekyll Errors: Check for plugin-related errors in _config.yml.
Wrong Branch or Directory: Confirm the correct branch and directory are selected in GitHub Pages settings.
Let me know if you need help debugging specific issues!







You said:
synonym for goal
ChatGPT said:
ChatGPT
Here are some synonyms for the word goal, depending on the context:

Objective
Aim
Target
Ambition
Aspiration
Purpose
Intention
End
Milestone
Mission
Destination
Resolution
Outcome
Dream
Agenda
Let me know if you need synonyms tailored to a specific context!







You said:
how to add beautiful math equations in github al -folio
ChatGPT said:
ChatGPT
To include beautiful math equations in your GitHub Al-Folio site, you can use MathJax, which is already supported by the Al-Folio theme. Here’s how to set it up and use it:

1. Enable MathJax in _config.yml
The Al-Folio theme includes support for MathJax by default. To ensure it’s enabled:

Open the _config.yml file in your repository.
Locate the math: section and set it to true:
yaml
Copy
Edit
math: true
2. Write Math Equations
You can write math equations in LaTeX syntax within your Markdown files. Use the following delimiters:

Inline Math
For inline math equations, use single $...$:

markdown
Copy
Edit
Here is an inline equation: $E=mc^2$
Block Math
For block math equations, use $$...$$:

markdown
Copy
Edit
Here is a block equation:
$$
\int_{a}^{b} f(x) \, dx = F(b) - F(a)
$$
3. Example Usage in Markdown
Here’s an example of a Markdown file with math equations:

markdown
Copy
Edit
---
layout: post
title: "Math Example"
math: true
---

This is an inline equation: $E = mc^2$.

This is a block equation:
$$
\sum_{n=1}^\infty \frac{1}{n^2} = \frac{\pi^2}{6}
$$
4. Styling Math Equations
You can customize how equations are rendered by modifying the MathJax configuration. Add or edit the MathJax configuration script in your _includes/head_custom.html file.

Example Custom Configuration:
html
Copy
Edit
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: {
      inlineMath: [['$', '$'], ['\\(', '\\)']],
      displayMath: [['$$', '$$'], ['\\[', '\\]']],
      processEscapes: true
    }
  });
</script>
5. Test Locally
To test your changes locally:

Run the Jekyll server:
bash
Copy
Edit
bundle exec jekyll serve
Open your site at http://localhost:4000 and check that equations render correctly.
6. Commit and Deploy
Once the equations are rendering as desired:

Commit your changes:
bash
Copy
Edit
git add .
git commit -m "Enabled MathJax for equations"
Push to your repository:
bash
Copy
Edit
git push
Your equations will now render beautifully on your live site!

Let me know if you encounter any issues or need further assistance!







You said:
what is a markdown file
ChatGPT said:
ChatGPT
A Markdown file is a plain text file that uses Markdown syntax to format content in a simple and readable way. Markdown is widely used for writing documentation, web content, and simple text-based formats. Files written in Markdown typically have a .md or .markdown extension.

Features of Markdown
Markdown is designed to be easy to write and read. It converts text with simple syntax into formatted HTML when rendered.

Common Markdown Elements:
Headings:

markdown
Copy
Edit
# Heading 1
## Heading 2
### Heading 3
Bold and Italics:

markdown
Copy
Edit
**Bold Text**
*Italic Text*
Lists:

Unordered list:
markdown
Copy
Edit
- Item 1
- Item 2
- Item 3
Ordered list:
markdown
Copy
Edit
1. First item
2. Second item
Links:

markdown
Copy
Edit
[Link text](https://example.com)
Images:

markdown
Copy
Edit
![Alt text](image-url.jpg)
Code Blocks:

Inline code:
markdown
Copy
Edit
`print('Hello, world!')`
Block code:
markdown
Copy
Edit
def hello(): print("Hello, world!")
Copy
Edit
Blockquotes:

markdown
Copy
Edit
> This is a quote.
Tables:

markdown
Copy
Edit
| Header 1 | Header 2 |
|----------|----------|
| Row 1    | Data 1   |
| Row 2    | Data 2   |
Where Markdown is Used
Documentation: README.md files in GitHub repositories.
Static Site Generators: Tools like Jekyll, Hugo, and Gatsby use Markdown for writing content.
Blogs: Markdown is often used in blogging platforms like WordPress and Ghost.
Notes: Apps like Notion, Obsidian, and Evernote support Markdown for formatting.
Benefits of Markdown
Simplicity: Easy to write and read.
Portability: Can be used across different platforms and converted to HTML.
Flexibility: Supports plugins for additional features like math equations and diagrams.
Widely Supported: Used in GitHub, Jekyll, and many other platforms.
Let me know if you'd like examples or need help using Markdown!







You said:
is it a .md file
ChatGPT said:
ChatGPT
Yes, a Markdown file typically has the .md file extension, though it can also use .markdown. For example:

README.md
about.md
documentation.markdown
Both .md and .markdown are recognized as Markdown files. The shorter .md extension is more commonly used today, especially in platforms like GitHub, where README.md is standard for repository documentation.

If you're working with GitHub Pages or static site generators like Jekyll (used in Al-Folio), the content files are often written in Markdown and saved with a .md extension. These files are then converted into HTML during the build process.

Let me know if you need help creating or editing a .md file!







You said:
can I write \dot v =\rho in .md file and how?
ChatGPT said:
ChatGPT
Yes, you can write 
𝑣
˙
=
𝜌
v
˙
 =ρ in a .md file by using MathJax or KaTeX, which render LaTeX-style math equations in Markdown files. This is common in static site generators like Jekyll (used in Al-Folio). Here's how to do it:

1. Enable MathJax in Your Jekyll Configuration
Make sure MathJax is enabled in your _config.yml file:

yaml
Copy
Edit
math: true
If you're using Al-Folio, MathJax support is already included, and setting math: true should suffice.

2. Write the Equation in Markdown
Inline Equation:
For inline math, wrap the equation in single dollar signs ($...$):

markdown
Copy
Edit
The equation of motion is $ \dot{v} = \rho $.
Block Equation:
For block math, wrap the equation in double dollar signs ($$...$$):

markdown
Copy
Edit
The equation of motion is:
$$
\dot{v} = \rho
$$
3. Rendered Output
Inline Equation: 
𝑣
˙
=
𝜌
v
˙
 =ρ will appear inline with the text.
Block Equation:
𝑣
˙
=
𝜌
v
˙
 =ρ
will appear as a standalone block.
4. Commit and Deploy
Save the changes to your .md file.
Push the changes to your GitHub repository:
bash
Copy
Edit
git add .
git commit -m "Added math equation"
git push
5. Test Locally (Optional)
To verify that the math renders correctly:

Run your Jekyll site locally:
bash
Copy
Edit
bundle exec jekyll serve
Open your site in the browser at http://localhost:4000.
Notes:
MathJax must be enabled and properly configured in your Jekyll theme for this to work.
For standalone Markdown files viewed directly (e.g., in a text editor), the math may not render unless the viewer supports MathJax or LaTeX.
Let me know if you need further help!







You said:
adding a heading on a al-folio page
ChatGPT said:
ChatGPT
Adding a heading to a page in Al-Folio (or any Jekyll-based site) is straightforward. You can use Markdown syntax within the .md file for the page to add a heading. Here's how to do it:

1. Open the Page's Markdown File
Locate the .md file of the page where you want to add the heading. For example, if it's the About page, the file is likely _pages/about.md.

2. Add a Heading Using Markdown
Use Markdown syntax for headings. The number of # symbols defines the heading level:

# for a top-level heading (H1)
## for a second-level heading (H2)
### for a third-level heading (H3), and so on.
Example:
markdown
Copy
Edit
# Heading Level 1
This is the main heading.

## Heading Level 2
This is a subheading.

### Heading Level 3
This is a smaller subheading.
3. Customize the Heading (Optional)
You can use HTML directly if you want to add custom styles or classes to the heading:

Example with HTML:
markdown
Copy
Edit
<h1 class="custom-class">Custom Styled Heading</h1>
4. Save and Preview Locally
Save the changes to the .md file.
If you're working locally, run your site to see how the headings look:
bash
Copy
Edit
bundle exec jekyll serve
Open your browser at http://localhost:4000 and navigate to the page.
5. Push and Deploy
Once you're happy with the heading:

Commit and push your changes:
bash
Copy
Edit
git add .
git commit -m "Added heading to the About page"
git push
Result
The heading(s) will now appear on your Al-Folio page. Markdown automatically converts them into HTML during the build process.

Let me know if you need more assistance!







You said:
how to remove the selected publications portion on the about page in al folio github websites
ChatGPT said:
ChatGPT
To remove the Selected Publications section from the "About" page in your Al-Folio GitHub website, you can follow these steps:

1. Edit the _pages/about.md File
Locate the about.md file in the _pages directory of your repository. This file controls the content of the "About" page.

Open _pages/about.md.
Find the following line in the YAML front matter:
yaml
Copy
Edit
selected_papers: true
Set it to false to disable the Selected Publications section:
yaml
Copy
Edit
selected_papers: false
2. Save and Commit Changes
After making the change:

Save the file.
Commit the changes:
bash
Copy
Edit
git add _pages/about.md
git commit -m "Removed Selected Publications from About page"
git push
3. Verify Changes
If you're testing locally, run the Jekyll server:

bash
Copy
Edit
bundle exec jekyll serve
Open your browser at http://localhost:4000 and confirm the Selected Publications section is no longer visible.

If working directly on GitHub, push the changes and wait for the GitHub Pages deployment. Then check your live site.

Optional: Completely Remove the Section
If you want to remove the Selected Publications logic from your site entirely:

Open the layout file for the About page (usually _layouts/about.html).
Locate and remove the block of code that renders the selected papers. It typically looks like this:
html
Copy
Edit
{% if page.selected_papers %}
  <section id="selected-publications">
    <!-- Code to display publications -->
  </section>
{% endif %}
Save and commit the file.
Let me know if you encounter any issues!







You said:
list in .md file
ChatGPT said:
ChatGPT
You can create lists in a .md (Markdown) file using simple syntax. Markdown supports ordered lists, unordered lists, and nested lists. Here's how to create them:

1. Unordered Lists
Use -, *, or + to create unordered lists.

Example:
markdown
Copy
Edit
- Item 1
- Item 2
- Item 3
Output:
Item 1
Item 2
Item 3
2. Ordered Lists
Use numbers followed by a period (1., 2., etc.) for ordered lists.

Example:
markdown
Copy
Edit
1. First item
2. Second item
3. Third item
Output:
First item
Second item
Third item
3. Nested Lists
Indent with two spaces (or a tab) to create nested lists.

Example:
markdown
Copy
Edit
- Main item 1
  - Subitem 1.1
  - Subitem 1.2
- Main item 2
  1. Subitem 2.1
  2. Subitem 2.2
Output:
Main item 1
Subitem 1.1
Subitem 1.2
Main item 2
Subitem 2.1
Subitem 2.2
4. Mixed Lists
You can combine ordered and unordered lists.

Example:
markdown
Copy
Edit
1. Ordered item
   - Unordered subitem
   - Another unordered subitem
2. Another ordered item
Output:
Ordered item
Unordered subitem
Another unordered subitem
Another ordered item
5. Add Content to List Items
You can include additional content like text, links, or even code in list items.

Example:
markdown
Copy
Edit
- [Markdown Guide](https://www.markdownguide.org) - A helpful resource.
- Use `code` in a list item for clarity.
- Add **bold** or *italic* text.
Output:
Markdown Guide - A helpful resource.
Use code in a list item for clarity.
Add bold or italic text.
Markdown lists are versatile and easy to use. Let me know if you need help with a specific layout!







You said:
<div class="row">
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/error_di_ts0.02_page-0001.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/t_di_err_ts0.02_page-0001.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
   Left: Comparison of the position, velocity, and input trajectories for the three MPC schemes subject to an additive disturbance on the input. Right: The computation time for the three schemes with noise, compared to the sampling time ts = 0.02. HMPC is the only scheme that consistently satisfies the real-time requirements.
</div> 


modify this code so that the picture I insert is only on the left side of the page and i can wrap text arounds it 
ChatGPT said:
ChatGPT
To modify the code so that the image appears on the left side of the page and allows text to wrap around it, you can use a layout with a floating image and text. Here's the modified code:

<div class="row">
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0" style="float: left; margin-right: 15px;">
        {% include figure.liquid loading="eager" path="assets/img/lane_change.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div>
        <p>
            Left: Comparison of the position, velocity, and input trajectories for the three MPC schemes subject to an additive disturbance on the input. 
            The image is displayed on the left side of the page with text wrapping around it. You can continue adding more paragraphs or details here.
        </p>
        <p>
            Right: The computation time for the three schemes with noise, compared to the sampling time ts = 0.02. HMPC is the only scheme that 
            consistently satisfies the real-time requirements.
        </p>
    </div>
</div>
The disadvantage of MPC1 becomes apparent by examining the computation time required to solve the underlying
OCP. As shown, the time required to solve
MPC1 is approximately 30 times more than HMPC and
MPC2. This is because MPC1 has a prediction length of
N = 100 steps, whereas HMPC and MPC2 have a prediction
length of only N = 5 steps.









