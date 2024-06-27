import tkinter as tk
from tkinter import ttk, messagebox
import requests


class MusicApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Music Downloader")

        self.artist_label = tk.Label(root, text="Artist Name:")
        self.artist_label.pack()

        self.artist_entry = tk.Entry(root)
        self.artist_entry.pack()

        self.search_button = tk.Button(root, text="Search", command=self.search_songs)
        self.search_button.pack()

        self.song_combo = ttk.Combobox(root, state="readonly")
        self.song_combo.pack()

        self.download_button = tk.Button(root, text="Download", command=self.download_song)
        self.download_button.pack()

        self.songs = []

    def search_songs(self):
        artist = self.artist_entry.get()
        if not artist:
            messagebox.showerror("Error", "Please enter an artist name.")
            return

        # Use the Shazam API to fetch songs by the artist
        self.songs = self.fetch_songs_by_artist(artist)

        if not self.songs:
            messagebox.showinfo("No Songs", "No songs found for this artist.")
            return

        self.song_combo['values'] = [song['track']['title'] for song in self.songs]
        self.song_combo.current(0)

    def fetch_songs_by_artist(self, artist):
        url = "https://shazam.p.rapidapi.com/search"  #please include your URL
        querystring = {"term": artist, "locale": "en-US", "offset": "0", "limit": "20"}
        headers = {
            "x-rapidapi-key": " ", #please include your x-rapidapi-key
            "x-rapidapi-host": " " #please include your x-rapidapi-host
        }

        try:
            response = requests.get(url, headers=headers, params=querystring)
            response.raise_for_status()  # Raise an error for bad status codes
            return response.json().get('tracks', {}).get('hits', [])
        except requests.exceptions.RequestException as e:
            messagebox.showerror("Error", f"Failed to fetch songs: {e}")
            return []

    def get_song_url(self, song_title):
        for song in self.songs:
            if song['track']['title'] == song_title:
                for action in song['track']['hub']['actions']:
                    if action['type'] == 'uri':
                        return action['uri']
        return None

    def download_song(self):
        selected_song = self.song_combo.get()
        if not selected_song:
            messagebox.showerror("Error", "Please select a song to download.")
            return

        song_url = self.get_song_url(selected_song)
        if song_url:
            try:
                response = requests.get(song_url)
                response.raise_for_status()  # Raise an error for bad status codes
                with open(f"{selected_song}.mp3", "wb") as file:
                    file.write(response.content)
                messagebox.showinfo("Success", f"Downloaded {selected_song}.mp3")
            except requests.exceptions.RequestException as e:
                messagebox.showerror("Error", f"Failed to download song: {e}")
        else:
            messagebox.showerror("Error", "Could not find the URL for the selected song.")


if __name__ == "__main__":
    root = tk.Tk()
    app = MusicApp(root)
    root.mainloop()
