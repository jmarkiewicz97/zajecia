from Tkinter import PhotoImage
from ttk import Label

from PIL import Image, ImageTk

    
class AnimatedGIF(Label, object):
    def __init__(self, master, path_to_gif):
        self._master = master
        self._loc = 0

        im = Image.open(path_to_gif)
        self._frames = []
        i = 0
        try:
            while True:
                temp = im.copy()
                self._frames.append(ImageTk.PhotoImage(temp.convert('RGBA')))

                i += 1
                im.seek(i)
        except EOFError: pass
        
        self._len = len(self._frames)

        try:
            self._delay = im.info['duration']
        except:
            self._delay = 100

        self._callback_id = None

        super(AnimatedGIF, self).__init__(master, image=self._frames[0])
    
    def _run(self):
        self._loc += 1
        if self._loc == self._len:
            self._loc = 0

        self.configure(image=self._frames[self._loc])
        self._callback_id = self._master.after(self._delay, self._run)

    def pack(self, *args, **kwargs):
        self._run()
        super(AnimatedGIF, self).pack(*args, **kwargs)

    def grid(self, *args, **kwargs):
        self._run()
        super(AnimatedGIF, self).grid(*args, **kwargs)
        
    def place(self, *args, **kwargs):
        self._run()
        super(AnimatedGIF, self).place(*args, **kwargs)

if __name__ == "__main__":
    from Tkinter import Tk, Label

    root = Tk()
    
    # Add the path to a GIF to make the example working
    l = AnimatedGIF(root, "path to gif")
    l.pack()

    root.mainloop()