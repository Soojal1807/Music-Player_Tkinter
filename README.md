import os
import pygame    
from tkinter import Tk, Listbox, Button, filedialog, Scale   

class MusicPlayer:
    def __init__(self, master):                                #For PlayerBox and you can change box size according to you by making changes in 9th line.
        self.master = master
        self.master.title("Music Player")
        self.master.geometry("400x300")

        self.playlist = Listbox(self.master, selectmode='SINGLE')         
        self.playlist.pack(pady=10)

        self.load_button = Button(self.master, text="Load Songs", command=self.load_songs)          
        self.load_button.pack(pady=5)

        self.play_button = Button(self.master, text="Play", command=self.play_music)
        self.play_button.pack(pady=5)

        self.stop_button = Button(self.master, text="Stop", command=self.stop_music)
        self.stop_button.pack(pady=5)

        self.pause_button = Button(self.master, text="Pause", command=self.pause_music)
        self.pause_button.pack(pady=5)

        self.volume_scale = Scale(self.master, from_=0, to=100, orient='horizontal', command=self.set_volume)
        self.volume_scale.set(50)  # Set default volume to 50
        self.volume_scale.pack(pady=5)

        self.file_paths = []  
        pygame.mixer.init()

    def load_songs(self):
        file_paths = filedialog.askopenfilenames(filetypes=[("MP3 Files", "*.mp3")])      #This help in only loadind mp3 files
        if file_paths:
            self.playlist.delete(0, 'end')
            self.file_paths = file_paths  
            for file_path in file_paths:
                song = os.path.basename(file_path)
                self.playlist.insert('end', song)

    def play_music(self):
        current_selection = self.playlist.curselection()                   #Play the selected song.
        if current_selection:
            song_path = self.file_paths[current_selection[0]]
            pygame.mixer.music.load(song_path)
            pygame.mixer.music.play()

    def stop_music(self):
        pygame.mixer.music.stop()             #Stop the selected song.

    def pause_music(self):
        pygame.mixer.music.pause()               #Pause the selected song.

    def set_volume(self, val):                         #Increase or Decrease volume.
        volume = int(val) / 100.0
        pygame.mixer.music.set_volume(volume)

if __name__ == "__main__":
    root = Tk()
    app = MusicPlayer(root)
    root.mainloop()

