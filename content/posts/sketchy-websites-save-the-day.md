---
title: "Sketchy Websites Save the Day"
date: 2022-09-07T23:04:12-05:00
description: "Salvaging an old HP LaserJet printer by downloading sketchy files from sketchy websites"
type: "post"
tags: ["fun", "printer", "sketchy", "firmware", "security"]
---


I tried salvaging an old HP LaserJet 600 M601 printer (it's from 2011, probably earlier) today, and it was... sketchy, to say the least.

The printer turned on perfectly, but the web interface still uses TLS 1.1 and a self-signed certificate, so firmware update time! (You can also enable TLS 1.1 support in your browser if you like security vulnerabilities.)

This printer is so old you can't find the firmware files online, but I did finally locate a copy from a [random sketchy YouTube video](https://www.youtube.com/watch?v=dRynPlcOOWM) which links to a Google Drive zip file containing another zip file containing the firmware. Hey, not as bad as [this xkcd](https://xkcd.com/1247/)!

Next, I formatted a USB drive with a MS-DOS partition table (GPT is too new I guess) and a FAT32 partition and copied the firmware to that partition. I plugged the USB drive into the printer and flashed the new firmware and rebooted. Somehow, it worked perfectly!

Except one caveat: the new firmware disabled the print page in the web interface for some reason. Fortunately, there's [a solution for that on HP's website even though most of their other stuff for this printer have been taken down](https://support.hp.com/us-en/document/c047436990). Unfortunately, it requires the printer's admin password.

Oh crap, time to brute-force the password. Not admin. Not password. Not 1234. Not the empty string. Not 10060311 which I found in [another sketchy YouTube video](https://www.youtube.com/watch?v=zOp4DpJEq8Q). Even this huge [sketchy list](https://theinfocentric.com/hp-printer-default-password/) didn't help.

Welp, the only other option is to reset it.

So you can't actually find the instructions on how to reset the admin password anywhere on HP's website. My only clue was [this cryptic post](https://h30434.www3.hp.com/t5/LaserJet-Printing/reset-of-the-password-cold-reset-of-a-Laserjet-600-M601/td-p/5494913), telling me to track down the printer's service manual.

So that's when things got really sketchy. You can find the service manual online in a few places, but they're all paywalled with sketchy credit-card-number-stealing paywalls. Except for one site. It might have a sketchy domain name, and a weird Google Translate for Hungarian frame around the website, and a 2010-esque design, and a captcha, and a 5 file per day download limit, but it works: https://elektrotanya.com/hp_laserjet_enterprise_600_m601_m602_m603_sm.pdf/download.html#dl

On page 185 in the probably-machine-generated-572-page manual, there are instructions on how to access the super secret sketchy hidden preboot menu. Perfect! There's a setting buried in there to reset the admin password! Victory!

Except... it didn't work.

I tried many more times before admitting defeat... but I'm so close! Only one option left. Cold reset.

Also on page 185, there are much more dangerous instructions. The cold reset apparently wipes all your settings and data and maybe even the firmware, so I gave it a try, waited 10 minutes, and I was greated with a printer setup screen. After choosing 24-hour time and the ideologically correct date format, I'd finally vanquished the terrible admin password from the printer! Victory!

Another 50 pound block of e-waste salvaged!

