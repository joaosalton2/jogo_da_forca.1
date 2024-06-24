import tkinter as tk
import random as rnd

# Listas de palavras
frutas = ['Pera', 'Abacate', 'Banana', 'Laranja', 'Manga']
animais = ['Macaco', 'Peixe', 'Zebra', 'Tigre', 'Elefante', 'Lontra', 'Sapo', 'Cobra', 'Pelicano']
profissoes = ['Professor', 'Enfermeiro', 'Motorista', 'Advogado', 'Jornalista', 'Engenheiro', 'Dentista', 'Fisioterapeuta']

categorias = {
    'Frutas': frutas,
    'Animais': animais,
    'Profissões': profissoes
}

class Application:
    def __init__(self, root):
        self.root = root
        self.iniciar_jogo()
        self.tela()
        self.frames_da_tela()
        self.criando_widgets()
        self.atualizar_palavra()

    def tela(self):
        self.root.title("Jogo da Forca")
        self.root.configure(background='#E9967A')
        self.root.geometry("1000x850")
        self.root.resizable(True, True)
        self.root.minsize(width=400, height=300)

    def frames_da_tela(self):
        self.frame1 = tk.Frame(self.root, bg='#FFDAB9')
        self.frame1.place(relx=0.1, rely=0.1, relwidth=0.8, relheight=0.8)

    def criando_widgets(self):
        self.label_categoria = tk.Label(self.frame1, text=f"Categoria: {self.categoria}", font=('Arial', 20), bg='#FFDAB9')
        self.label_categoria.pack(pady=10)

        self.label_palavra = tk.Label(self.frame1, text="", font=('Arial', 30), bg='#FFDAB9')
        self.label_palavra.pack(pady=10)

        self.label_chances = tk.Label(self.frame1, text=f"Chances restantes: {self.chances}", font=('Arial', 20), bg='#FFDAB9')
        self.label_chances.pack(pady=10)

        self.entrada_letra = tk.Entry(self.frame1, font=('Arial', 20))
        self.entrada_letra.pack(pady=10)

        self.botao_tentar = tk.Button(self.frame1, text="Tentar", command=self.tentar_letra, font=('Arial', 20), bg='#FF7F50')
        self.botao_tentar.pack(pady=10)

        self.label_mensagem = tk.Label(self.frame1, text="", font=('Arial', 20), bg='#FFDAB9')
        self.label_mensagem.pack(pady=10)

        self.botao_jogar_novamente = tk.Button(self.frame1, text="Jogar Novamente", command=self.jogar_novamente, font=('Arial', 20), bg='#FF7F50')
        self.botao_jogar_novamente.pack(pady=10)

        self.canvas = tk.Canvas(self.frame1, width=200, height=200, bg='#FFDAB9')
        self.canvas.pack(pady=20)

    def iniciar_jogo(self):
        self.categoria, self.palavra = self.escolher_palavra()
        self.letras_usuario = []
        self.chances = 10
        self.ganhou = False

    def escolher_palavra(self):
        categoria = rnd.choice(list(categorias.keys()))
        palavra = rnd.choice(categorias[categoria]).lower()
        return categoria, palavra

    def atualizar_palavra(self):
        exibicao_palavra = ''
        for letra in self.palavra:
            if letra in self.letras_usuario:
                exibicao_palavra += letra + ' '
            else:
                exibicao_palavra += '_ '
        self.label_palavra.config(text=exibicao_palavra)
        self.label_categoria.config(text=f"Categoria: {self.categoria}")
        self.label_chances.config(text=f"Chances restantes: {self.chances}")
        self.desenhar_boneco()

    def desenhar_boneco(self):
        self.canvas.delete("all")
        if self.chances <= 9:
            self.canvas.create_line(10, 190, 190, 190)  # Base
        if self.chances <= 8:
            self.canvas.create_line(50, 190, 50, 20)  # Poste
        if self.chances <= 7:
            self.canvas.create_line(50, 20, 100, 20)  # Haste superior
        if self.chances <= 6:
            self.canvas.create_line(100, 20, 100, 30)  # Haste inferior
        if self.chances <= 5:
            self.canvas.create_oval(70, 40, 130, 120)  # Cabeça
        if self.chances <= 4:
            self.canvas.create_oval(85, 55, 95, 65)  # Olho esquerdo
            self.canvas.create_oval(105, 55, 115, 65)  # Olho direito
        if self.chances <= 3:
            self.canvas.create_line(90, 80, 110, 80)  # Boca
        if self.chances <= 2:
            self.canvas.create_line(100, 100, 100, 160)  # Corpo
        if self.chances <= 1:
            self.canvas.create_line(100, 110, 70, 140)  # Braço esquerdo
            self.canvas.create_line(100, 110, 130, 140)  # Braço direito
        if self.chances == 0:
            self.canvas.create_line(100, 160, 70, 190)  # Perna esquerda
            self.canvas.create_line(100, 160, 130, 190)  # Perna direita

    def tentar_letra(self):
        tentativa = self.entrada_letra.get().lower()
        self.entrada_letra.delete(0, tk.END)

        if tentativa in self.letras_usuario:
            self.label_mensagem.config(text="Você já tentou essa letra. Escolha outra.")
            return

        self.letras_usuario.append(tentativa)

        if tentativa not in self.palavra:
            self.chances -= 1
            self.label_chances.config(text=f"Chances restantes: {self.chances}")

        self.atualizar_palavra()

        if self.chances == 0:
            self.label_mensagem.config(text=f"Você perdeu! A palavra era {self.palavra.capitalize()}")
            self.botao_tentar.config(state=tk.DISABLED)
            return

        self.ganhou = all(letra in self.letras_usuario for letra in self.palavra)

        if self.ganhou:
            self.label_mensagem.config(text=f"Parabéns, você ganhou! A palavra era {self.palavra.capitalize()}")
            self.botao_tentar.config(state=tk.DISABLED)

    def jogar_novamente(self):
        self.iniciar_jogo()
        self.atualizar_palavra()
        self.label_mensagem.config(text="")
        self.botao_tentar.config(state=tk.NORMAL)

if __name__ == "__main__":
    root = tk.Tk()
    Application(root)
    root.mainloop()
