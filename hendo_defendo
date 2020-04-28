#!/usr/bin/env python3

# hendo defendo:
# an Etonian take on whack-a-mole

# this is v2 of the program, v1 got corrupted and i had to start over
# i wrote this in an hour flat though so it probably doesn't work

# weird tk capitalisation
try:
    from tkinter import *
    from tkinter import messagebox
except ImportError:
    from Tkinter import *
    from Tkinter import messagebox

# imports
import random
from PIL import Image, ImageTk
import os
import os.path as path
from termcolor import colored


# mAiN cLaSs
class Game():
    def __init__(self):
        # constants n dat
        self.debug = False
        self.IMAGE_PREFIX = None
        self.GRID_SIZE = 4
        self.boater_image = 'boater.png'
        self.bojo_image = 'boris.png'
        self.icon_file = 'icon.ico'
        self.hide_file = 'hide.png'
        self.MIN_TIME_DN = 2000
        self.MAX_TIME_DN = 9000
        self.MIN_TIME_UP = 1000
        self.MAX_TIME_UP = 3000
        self.BOATER_PROBABILITY = 0.07
        self.BOJO_PROBABILITY = 0.2

        # init
        self.msg('Initializing...')
        self.window = Tk()

        # this is pretty interesting, Pillow likes us to keep a
        # reference to every image object we create, so they get
        # chucked in here
        self.image_objects = []

        self.label_timers = {}

        if not self.IMAGE_PREFIX:
            self.IMAGE_PREFIX = os.getcwd()+'/media/'
        self.hendo_img_prefix = self.IMAGE_PREFIX+'hendos/'
        # we like percentages
        self.BOATER_PROBABILITY = int(self.BOATER_PROBABILITY*100)
        self.BOJO_PROBABILITY = int(self.BOJO_PROBABILITY*100)
        self.msg('Boater prob:  '+str(self.BOATER_PROBABILITY)+'%\nBojo prob:  '+str(self.BOJO_PROBABILITY)+'%')

        # automatically discover images of Hendo
        self.hendo_images = []
        for file in os.listdir(self.hendo_img_prefix):
            self.msg('Discovered '+str(file))
            self.hendo_images.append(self.hendo_img_prefix+str(file))

        # if we didn't pull anything there the user is being strange, attempt
        # to discover any in the normal media dir
        if len(self.hendo_images) == 0:
            print('Warning: images of Hendo should be located under '+self.hendo_img_prefix)
            for file in os.listdir(self.IMAGE_PREFIX):
                fname, extension = path.splitext(file)
                if ('hendo' in fname) and (extension in ['.jpg','.jpeg','.png','.gif']):
                    self.msg('Discovered '+file+' in '+self.IMAGE_PREFIX)
                    self.hendo_images.append(self.IMAGE_PREFIX+file)
            print('Discovered '+str(len(self.hendo_images))+' images of Hendo.')


        self.window.title('Hendo Defendo')
        self.icon_path = self.IMAGE_PREFIX + self.icon_file
        self.icon_img = ImageTk.PhotoImage(Image.open(self.icon_path))
        self.image_objects.append(self.icon_img)
        self.window.tk.call('wm','iconphoto',self.window._w,self.icon_img)

        self.boater_labels = []
        self.bojo_labels = []

        self.hendos_frame, self.info_frame = self.create_frames()
        self.hidden_hendos = []
        self.hendos = self.create_hendos()
        self.hit_count, self.miss_count, self.start_btn, self.quit_btn, self.help_btn, self.loss_label = self.create_info_widgets()
        self.set_callbacks()
        self.game_running = False


        self.msg('Initialized.')

    # print if debug=True
    def msg(self,msg):
        if self.debug:
            print(msg)

    # use seeding to generate a bool based on a given probability
    # TAKES A PERCENTAGE
    def prob_bool(self,probability=50):
        self.msg('prob_bool, probability = '+str(probability))
        seeds = []
        for i in range(probability):
            current = random.randint(1,100)
            while current in seeds:
                current = random.randint(1,100)
            seeds.append(current)

        if random.randint(1,100) in seeds:
            self.msg('prob_bool is True')
            return True
        else:
            self.msg('prob_bool is False')
            return False

    # initialize tk frames
    def create_frames(self):
        self.msg('Creating frames...')
        hendos_frame = Frame(self.window, bg='red', width=300, height=300)
        hendos_frame.grid(row=1, column = 1)

        info_frame = Frame(self.window, bg='white', width = 100, height = 300)
        info_frame.grid(row=1, column = 2)

        return hendos_frame, info_frame

    # thought there were only gonna be a couple of these so each got their own function,
    # on balance i should've defined one function to generate a PhotoImage from a given file path,
    # appending it to self.image_objects etc etc

    # generate a PIL.ImageTk.PhotoImage of Hendo from our hendo_images list
    def generate_hendo(self):
        self.msg('Generating a PhotoImage of Hendo')
        hendo_file = random.choice(self.hendo_images)
        hendo_img = ImageTk.PhotoImage(Image.open(hendo_file))
        self.image_objects.append(hendo_img)
        return hendo_img

    # generate a PhotoImage of our hide_file
    def generate_hidden_hendo(self):
        self.msg('Generating a PhotoImage of Hidden Hendo')
        hidden_file = self.IMAGE_PREFIX + self.hide_file
        hidden_img = ImageTk.PhotoImage(Image.open(hidden_file))
        self.image_objects.append(hidden_img)
        return hidden_img

    # generate PhotoImage of bojo
    def generate_lord_and_master(self):
        self.msg('Generating a PhotoImage of our Lord and Master')
        bojo_file = self.IMAGE_PREFIX + self.bojo_image
        bojo_img = ImageTk.PhotoImage(Image.open(bojo_file))
        self.image_objects.append(bojo_img)
        return bojo_img

    # generate some scum
    def generate_scum(self):
        self.msg('Generating a PhotoImage of some scum *spits*')
        boater_file = self.IMAGE_PREFIX+self.boater_image
        boater_img = ImageTk.PhotoImage(Image.open(boater_file))
        self.image_objects.append(boater_img)
        return boater_img


    # initialize self.hendos with some Labels
    # Labels not Buttons cos they look nicer and it's rly
    # hard to get the widget from a ButtonPress (or so i'm told)
    def create_hendos(self):
        self.msg('Generating hendos')
        hendos = []
        for r in range(self.GRID_SIZE):
            hendo_row = []
            for c in range(self.GRID_SIZE):
                current_hendo = Label(self.hendos_frame, image = self.generate_hendo())
                current_hendo.grid(row = r, column = c, sticky = E+W+N+S)
                self.label_timers[id(current_hendo)] = None

                hendo_row.append(current_hendo)
            hendos.append(hendo_row)
        return hendos

    # init the widgets in the info_frame , return the relevant ones
    def create_info_widgets(self):
        self.msg('Generating widgets for the Info frame')
        spacer = Label(self.info_frame, text='', bg = 'white')
        spacer.pack(side='top', fill = Y, expand=True)

        # set text here so we can just change bg to black when we
        # want to display text
        loss_label = Label(self.info_frame, text='You lose!', bg = 'white')
        loss_label.pack(side='top', fill = Y, expand=True)

        spacer = Label(self.info_frame, text='', bg = 'white')
        spacer.pack(side='top', fill = Y, expand=True)

        hit_label = Label(self.info_frame, text='Hits:', bg = 'black')
        hit_label.pack(side='top', fill = Y, expand=True)

        hit_count = Label(self.info_frame, text='0', bg = 'black')
        hit_count.pack(side='top', fill = Y, expand=True)

        spacer = Label(self.info_frame, text='', bg = 'white')
        spacer.pack(side='top', fill = Y, expand=True)

        miss_label = Label(self.info_frame, text='Misses:', bg = 'black')
        miss_label.pack(side='top', fill = Y, expand=True)

        miss_count = Label(self.info_frame, text='0', bg = 'black')
        miss_count.pack(side='top', fill = Y, expand=True)

        spacer = Label(self.info_frame, text='', bg = 'white')
        spacer.pack(side='top', fill = Y, expand=True)

        start_btn = Button(self.info_frame, text='Start')
        start_btn.pack(side='top', fill = Y, expand = True, ipadx = 10)

        spacer = Label(self.info_frame, text='', bg = 'white')
        spacer.pack(side='top', fill = Y, expand=True)

        quit_btn = Button(self.info_frame, text='Quit')
        quit_btn.pack(side='top', fill = Y, expand = True, ipadx = 10)

        spacer = Label(self.info_frame, text='', bg = 'white')
        spacer.pack(side='top', fill = Y, expand=True)

        help_btn = Button(self.info_frame, text='Help!')
        help_btn.pack(side='top', fill = Y, expand = True, ipadx = 10)

        spacer = Label(self.info_frame, text='', bg = 'white')
        spacer.pack(side='top', fill = Y, expand=True)

        return hit_count, miss_count, start_btn, quit_btn, help_btn, loss_label

    # set callbacks for start/quit/help buttons & all hendo labels
    def set_callbacks(self):
        self.msg('Setting callbacks')
        for r in range(self.GRID_SIZE):
            for c in range(self.GRID_SIZE):
                self.hendos[r][c].bind('<ButtonPress-1>',self.hendo_hit)

        self.start_btn['command'] = self.start
        self.quit_btn['command'] = self.quit
        self.help_btn['command'] = self.help

    # callback when a hendo label is clicked
    def hendo_hit(self, event):
        self.msg(random.choice(['boop','wack','slap','ouchie']))
        if self.game_running:
            hendo = event.widget
            if hendo in self.hidden_hendos:
                self.msg(colored('Miss!','red'))
                self.miss_count['text'] = str(int(self.miss_count['text']) + 1)
            else:
                self.msg(colored('Hit!','green'))
                if hendo in self.boater_labels:
                    self.msg(colored('Boater!','green',attrs=['bold']))
                    self.hit_count['text'] = str(int(self.hit_count['text']) + 10)
                    self.hide_hendo(hendo, False)
                elif hendo in self.bojo_labels:
                    self.msg(colored('Oh no! You\'ve killed Boris!','red',attrs=['bold']))
                    self.lose()
                else:
                    self.hit_count['text'] = str(int(self.hit_count['text']) + 1)
                    self.hide_hendo(hendo, False)

    # bad naming, callback for the start/stop button
    def start(self):
        if self.start_btn['text'] == 'Start':
            self.msg('Start')
            for r in range(self.GRID_SIZE):
                for c in range(self.GRID_SIZE):
                    current_hendo = self.hendos[r][c]
                    current_hendo['image'] = self.generate_hidden_hendo()
                    self.hidden_hendos.append(current_hendo)
                    time_dn = random.randint(self.MIN_TIME_DN, self.MAX_TIME_DN)
                    timer_obj = current_hendo.after(time_dn, self.show_hendo, current_hendo)
                    self.label_timers[id(current_hendo)] = timer_obj

            self.game_running = True
            self.start_btn['text'] = 'Stop'

            self.hit_count['text'] = '0'
            self.miss_count['text'] = '0'
            self.loss_label['bg'] = 'white'

        else:
            self.msg('Stop')
            for r in range(self.GRID_SIZE):
                for c in range(self.GRID_SIZE):
                    current_hendo = self.hendos[r][c]
                    current_hendo['image'] = self.generate_hendo()
                    current_hendo.after_cancel(self.label_timers[id(current_hendo)])

            self.game_running = False
            self.hidden_hendos = []
            self.boater_labels = []
            self.bojo_labels = []
            self.start_btn['text'] = 'Start'

    # show a hendo
    # called by a hidden hendo's timer, probably
    # found in self.label_timers[id(self.hidden_hendos[your_hendo_here])]
    def show_hendo(self, hendo):
        self.msg('Showing hendo')
        boater = False
        bojo = False
        # usually both are available, we can't show both
        # so if we evaluate one before the other we get lots of bias
        # therefore we have to compute the choice randomly if both are
        # available
        boaterAvailable = False
        bojoAvailable = False
        tryBoater = False
        tryBojo = False
        if len(self.boater_labels) == 0:
            boaterAvailable = True
        if len(self.bojo_labels) == 0:
            bojoAvailable = True
        if bojoAvailable and (not boaterAvailable):
            self.msg('Might show a Bojo!')
            tryBojo = True
        elif boaterAvailable and (not bojoAvailable):
            self.msg('Might show a boater!')
            tryBoater = True
        elif (not boaterAvailable) and (not bojoAvailable):
            self.msg('Neither boater nor bojo is available.')
        else:
            self.msg('Boater and bojo are both available, deciding randomly.')
            # if given no probability the function decides 50/50
            if self.prob_bool():
                self.msg('Might show a boater!')
                tryBoater = True
            else:
                self.msg('Might show a Bojo!')
                tryBojo = True

        if tryBoater:
            self.msg('Trying a boater')
            boater = self.prob_bool(self.BOATER_PROBABILITY)
            if boater:
                self.msg('Showing a boater!')
            else:
                self.msg('Not showing a boater.')
        if tryBojo:
            self.msg('Trying a Bojo')
            bojo = self.prob_bool(self.BOJO_PROBABILITY)
            if bojo:
                self.msg('Showing a Bojo!')
            else:
                self.msg('Not showing a Bojo.')


        if boater:
            hendo['image'] = self.generate_scum()
            self.boater_labels.append(hendo)
        elif bojo:
            hendo['image'] = self.generate_lord_and_master()
            self.bojo_labels.append(hendo)
        else:
            hendo['image'] = self.generate_hendo()
        self.hidden_hendos.remove(hendo)
        if self.game_running:
            time_up = random.randint(self.MIN_TIME_UP, self.MAX_TIME_UP)
            timer_obj = hendo.after(time_up, self.hide_hendo, hendo, True)
            self.label_timers[id(hendo)] = timer_obj

        # if the whole square fills up with hendos, stop the game
        if len(self.hidden_hendos) == 0:
            self.msg('[WARNING] too much hendo')
            self.lose()

    # hide a hendo
    # also called by a hidden hendo's timer, with timer_expired = True
    # this gets called by hendo_hit as well, with timer_expired = False
    def hide_hendo(self, hendo, timer_expired):
        self.msg('Hiding hendo')
        if self.game_running:
            if timer_expired:
                if not (hendo in self.bojo_labels):
                    self.msg('Updating miss count')
                    self.miss_count['text'] = str(int(self.miss_count['text'])+1)
                else:
                    self.msg('Hiding a Bojo, miss_count won\'t be updated.')

            else:
                hendo.after_cancel(self.label_timers[id(hendo)])

            hendo['image'] = self.generate_hidden_hendo()
            self.hidden_hendos.append(hendo)
            if hendo in self.bojo_labels:
                self.msg('Removing Bojo from bojo_labels')
                self.bojo_labels.remove(hendo)
            elif hendo in self.boater_labels:
                self.msg('Removing boater from boater_labels')
                self.boater_labels.remove(hendo)

            time_dn = random.randint(self.MIN_TIME_DN, self.MAX_TIME_DN)
            timer_obj = hendo.after(time_dn, self.show_hendo, hendo)

            self.label_timers[id(hendo)] = timer_obj



    # callback for quit_btn
    def quit(self):
        self.msg('Quit?')
        confirm_quit = messagebox.askyesno('Quit?','Do you really want to quit?')
        if confirm_quit:
            self.msg('Quitting')
            if self.game_running:
                self.msg('Stopping game')
                self.start()
                self.msg('Showing score')
                messagebox.showinfo('Score','Hits:\n'+self.hit_count['text']+'\nMisses:\n'+self.miss_count['text'])
            self.msg('Destroying')
            self.window.destroy()

        else:
            self.msg('Staying!')

    # lose a game
    def lose(self):
        self.hit_count['text'] = '-69'
        self.miss_count['text'] = '-69'
        self.loss_label['bg'] = 'black'
        self.start()

    # callback for help_btn
    def help(self):
        self.msg('Help!')
        # stop the game if it's running
        if self.game_running:
            self.start()
        messagebox.showinfo('Help!','''
Hendo Defendo:
an Etonian take on whack-a-mole.

Play with a trackpad at
your own risk!

Press Start to begin.

When a Hendo appears, click it as
quickly as possible! If it disappears
before you click it, you've missed.
If you click a non-hendo square,
you've also missed.

Be warned - if the whole grid fills up with Hendos you lose
the game!

Special items:
Hit a Harrow Boater for 10 points!
If you hit a Boris, you lose.

''')



# mainloop, tried to add a bit of error handling but it's really
# ugly becasue messagebox initializes a new Tk window as well as the
# error window
def main():
    try:
        game = Game()
        game.window.mainloop()
    except Exception as e:
        messagebox.showerror('Error','An error occured.')
        raise e

if __name__ == '__main__':
    main()
