import os
import pygame
from tkinter import Tk, Listbox, Button, filedialog, Scale, StringVar, MULTIPLE, END, SINGLE, ACTIVE
import threading

class MusicPlayer:
    def __init__(self, master):
        self.master = master
        self.master.title("Simple Music Player")
        self.master.geometry("400x400")

        self.playlist = Listbox(
            self.master,
            selectmode=MULTIPLE,
            bd=5,
            relief="solid",
            bg="black",
            fg="green"
        )
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
        self.volume_scale.set(50)
        self.volume_scale.pack(pady=5)

        self.file_paths = []
        self.selected_files = StringVar()
        self.current_index = 0

        pygame.mixer.init()
        pygame.mixer.music.set_endevent(pygame.USEREVENT)

    def load_songs(self):
        file_paths = filedialog.askopenfilenames(filetypes=[("MP3 Files", "*.mp3")])
        if file_paths:
            self.playlist.delete(0, END)
            self.file_paths = file_paths
            for file_path in file_paths:
                song = os.path.basename(file_path)
                self.playlist.insert(END, song)

    def play_music(self):
        current_selection = self.playlist.curselection()
        if current_selection:
            selected_indices = self.playlist.curselection()
            selected_files = [self.file_paths[i] for i in selected_indices]
            self.selected_files.set(selected_files)
            self.play_selected_song(selected_files[0])
            self.clear_selection()

    def play_selected_song(self, song_path):
        pygame.mixer.music.load(song_path)
        pygame.mixer.music.play()
        threading.Thread(target=self.wait_for_song_end).start()

    def wait_for_song_end(self):
        while pygame.mixer.music.get_busy():
            pygame.time.Clock().tick(1)
        self.play_next_song()

    def play_next_song(self):
        self.current_index = (self.current_index + 1) % len(self.file_paths)
        next_song_path = self.file_paths[self.current_index]
        self.play_selected_song(next_song_path)

    def stop_music(self):
        pygame.mixer.music.stop()

    def pause_music(self):
        pygame.mixer.music.pause()

    def set_volume(self, val):
        volume = int(val) / 100.0
        pygame.mixer.music.set_volume(volume)

    def clear_selection(self):
        self.playlist.selection_clear(0, END)

if __name__ == "__main__":
    root = Tk()
    app = MusicPlayer(root)
    root.mainloop()
