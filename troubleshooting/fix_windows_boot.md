# Check Disk
chkdsk /f /r

# Fix Windows not booting
bootrec /fixmbr
bootrec /fixboot
bootrec /scanos
bootrec /rebuildbcd
