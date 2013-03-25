gntp.el
=======

GNTP implementation for Emacs. It's very incomplete, no encryption, no real error handling etc. but has just what I need. Feel free to improve it.

Usage
=====

This is the setup I use for rcirc

```lisp
;;; Growl notifications for IRC
(setq gntp-application-icon "C:/home/site-lisp/todochiku-icons/emacs_32.png")
(setq gntp-application-name "Emacs/gntp.el")
;;; register notifications, only needs to be done once per server
(let ((notifications
      '((irc-mention
           :display "IRC Mention"
           :enabled t
           :icon "C:/home/site-lisp/todochiku-icons/groupevent.png")
        (irc-convo
           :display "IRC Convo"
           :enabled t
           :icon "C:/home/site-lisp/todochiku-icons/chat.png"))))
 (gntp-register notifications "localhost"))

(defun gntp-convo-rcirc-print-hook (process sender response target text)
  (when (and (string= sender target)
             (not (string= (rcirc-nick process) sender))
             (not (string= (rcirc-server-name process) sender))
             (not (memq (rcirc-get-buffer process target t)
                        (rcirc-visible-buffers))))
    (gntp-notify 'irc-convo "IRC convo"
                 (format "<%s> %s" sender text) "localhost")))
(add-hook 'rcirc-print-hooks 'gntp-convo-rcirc-print-hook)

(defun gntp-rcirc-print-hook (process sender response target text)
  (when (and (rcirc-channel-p target)
             (string-match (rcirc-nick process) text)
             (not (string= (rcirc-nick process) sender))
             (not (string= (rcirc-server-name process) sender)))
    (gntp-notify 'irc-mention (format "IRC mention" target)
                 (format "%s <%s> %s" target sender text)
                 "localhost")))
(add-hook 'rcirc-print-hooks 'gntp-rcirc-print-hook)
```
