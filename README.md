# Debt Progress Tracker (installable app)

A private debt, income and savings tracker. It runs entirely on your own device: nothing is sent to a server, and every person who installs it has their own separate data.

## 1. Put it online (one time, free)
The app has to be served over HTTPS so phones and Macs can install it. Any static host works. Upload the contents of this folder as-is:

- **Netlify:** drag this folder onto Netlify Drop (app.netlify.com/drop), then claim the site with a free account so it stays up.
- **GitHub Pages:** put the files in a repository, then Settings > Pages > deploy from the main branch.
- **Cloudflare Pages:** create a project and upload the folder.

You get a link such as `https://your-name.netlify.app`. Send that link to friends and family.

## 2. Install it
**iPhone / iPad:** open the link in Safari, tap Share, then **Add to Home Screen**.
**Mac (Safari 17 or later):** open the link, then File > **Add to Dock**.
**Mac (Chrome or Edge):** click the install icon at the right of the address bar, or menu > Install.
**Android (bonus):** Chrome menu > Install app.

It opens full screen like a normal app and works offline.

## 3. Things everyone should know
- Data lives on that one device. Use the **Backup** tab to download a backup before switching phones, and to move data to another device.
- Clearing browser or website data deletes the app's data, so keep a backup.
- Each person starts with a blank tracker. Nobody can see anyone else's numbers.

## 4. Updating later
After changing `index.html`, change `VERSION` in `sw.js` (for example to `debt-tracker-v2`) and upload again. Installed copies update the next time they are opened online.
