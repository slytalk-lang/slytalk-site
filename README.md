# SlyTalk Website

Temporary placeholder website for [slytalk.org](https://slytalk.org/). All repository documentation is maintained in English.

## Repository layout

```text
slytalk-site/
├── .nojekyll
├── CNAME
├── README.md
├── index.html
├── robots.txt
└── assets/
    ├── favicon.svg
    ├── slytalk-appicon-dark.svg
    ├── slytalk-appicon-light.svg
    └── slytalk-logo.svg
```

This is a plain static site. Keep `index.html` at the repository root when publishing from the `main` branch.

## Publish with GitHub Pages

1. Open the repository: <https://github.com/slytalk-lang/slytalk-site>.
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**.
4. Select branch `main` and folder `/(root)`, then save.
5. In **Custom domain**, enter `slytalk.org` and save. Keep the `CNAME` file in the root.
6. After DNS has propagated and GitHub has issued the certificate, enable **Enforce HTTPS**.

The repository owner in the URL is `slytalk-lang`. The separate GitHub account `slytalk-dev` can be used to authenticate and commit, provided it has write access to this repository. A commit email does not grant repository permissions.

## Cloudflare DNS

First, add `slytalk.org` to Cloudflare and set the registrar's nameservers to the nameservers Cloudflare assigns to the zone. Wait until Cloudflare reports the zone as active.

In **Cloudflare → DNS → Records**, remove conflicting records for `@` or `www`, then configure:

| Type | Name | Target | Proxy |
|---|---|---|---|
| A | `@` | `185.199.108.153` | DNS only |
| A | `@` | `185.199.109.153` | DNS only |
| A | `@` | `185.199.110.153` | DNS only |
| A | `@` | `185.199.111.153` | DNS only |
| CNAME | `www` | `slytalk-lang.github.io` | DNS only |

These are GitHub Pages' documented apex IPv4 addresses. DNS-only is the safest initial setup while GitHub validates the custom domain and provisions HTTPS. Do not add a wildcard DNS record. GitHub recommends configuring the custom domain in repository settings before configuring DNS.

If you prefer IPv6, GitHub also publishes these apex AAAA addresses:
- `2606:50c0:8000::153`
- `2606:50c0:8001::153`
- `2606:50c0:8002::153`
- `2606:50c0:8003::153`

Once the site works over HTTPS, you can decide whether to enable Cloudflare proxying. If you do, review Cloudflare SSL/TLS settings and avoid modes that can cause redirect loops. DNS changes may take up to 24 hours to propagate.

Official references:
- GitHub Pages custom domains: <https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site>
- GitHub Pages HTTPS: <https://docs.github.com/en/pages/getting-started-with-github-pages/securing-your-github-pages-site-with-https>
- Cloudflare DNS: <https://developers.cloudflare.com/dns/>

## Keep the site out of search results for now

The page includes `noindex` directives for general crawlers, Googlebot, and Bingbot. `robots.txt` intentionally allows crawling so compliant search engines can fetch the page and see the `noindex` directives. A `robots.txt` disallow rule alone would not reliably remove a URL from search results.

Important limitations:
- `noindex` is a request to compliant search engines, not access control; the website remains public.
- Removal from search results is not immediate and cannot be guaranteed.
- Avoid sharing or linking the site publicly if you want to minimize discovery.
- Before the public launch, remove the `noindex` meta tags and revisit the description/social metadata.

## Use the SlyTalk GitHub identity on this repository

The commands below configure commit authorship for this repository only, leaving your existing global Git identity unchanged. Run them from inside the cloned repository:

```bash
git config user.name "SlyTalk"
git config user.email "hello@slytalk.org"
git config --local --get user.name
git config --local --get user.email
```

For commits to show as verified/contributed to the intended GitHub account, add and verify `hello@slytalk.org` in the email settings of the GitHub account that owns or should receive the contribution (likely `slytalk-dev`). This identity setting controls commit metadata only; authentication is configured separately.

### Recommended: separate SSH identity on macOS

If your existing GitHub account already uses SSH, create a second SSH key for the SlyTalk account:

```bash
ssh-keygen -t ed25519 -C "hello@slytalk.org" -f ~/.ssh/id_ed25519_slytalk
```

Add the **public** key (`~/.ssh/id_ed25519_slytalk.pub`) to the `slytalk-dev` GitHub account under **Settings → SSH and GPG keys**. Never upload or share the private key.

Add this host alias to `~/.ssh/config`:

```sshconfig
Host github-slytalk
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_slytalk
  IdentitiesOnly yes
```

Restrict the config file's permissions if needed:

```bash
chmod 600 ~/.ssh/config
```

Clone using the alias so Git uses the SlyTalk SSH key:

```bash
git clone git@github-slytalk:slytalk-lang/slytalk-site.git
cd slytalk-site
git config user.name "SlyTalk"
git config user.email "hello@slytalk.org"
```

For an already-cloned repository, update only its remote:

```bash
git remote set-url origin git@github-slytalk:slytalk-lang/slytalk-site.git
```

Test which GitHub account the alias authenticates as:

```bash
ssh -T git@github-slytalk
```

GitHub should greet the account associated with the added key. The `slytalk-dev` account must have write access to `slytalk-lang/slytalk-site` (directly or through organization membership/team access), otherwise pushes will be denied.

## Commit and push

After placing these files in the repository:

```bash
git add index.html assets CNAME robots.txt .nojekyll README.md
git commit -m "Add SlyTalk pre-release placeholder site"
git push -u origin main
```

If the repository already has content or a different default branch, inspect it before pushing and adapt the steps rather than overwriting existing work.
