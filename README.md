import random

# =========================================================
# Aula de Laboratório - Batalha Pokemon com Métodos (Python)
# Versão 3 (AVANÇADA): sobre a v2 (escolha de Pokemon +
# combustível por magia), adicionamos:
#   1) TIPOS e FRAQUEZAS: cada Pokemon tem um tipo, e golpes do
#      tipo vantajoso causam dano extra (ex: Fogo é super efetivo
#      contra Planta). O menu de escolha já mostra a fraqueza de
#      cada Pokemon antes de você decidir.
#   2) Pokemon adversário sorteado a cada partida (não só uma vez).
#   3) Loop "jogar novamente": ao final da batalha, o jogo pergunta
#      se você quer jogar outra partida, sem precisar reiniciar o
#      programa.
# =========================================================

# batalha.py
# Tabela de vantagens de tipo: chave = tipo do golpe, valor = tipo
# que esse golpe é super efetivo contra (causa 50% de dano a mais).
TABELA_VANTAGENS = {
    "Fogo": "Planta",
    "Planta": "Água",
    "Água": "Fogo",
    "Elétrico": "Água",
}


class Magia:
    """Representa um golpe/magia que um Pokemon pode usar."""

    def __init__(self, nome, poder, tipo, combustivel_maximo):
        self.nome = nome
        self.poder = poder
        self.tipo = tipo
        self.combustivel_maximo = combustivel_maximo
        self.combustivel_atual = combustivel_maximo

    # Método com retorno: diz se a magia ainda pode ser usada
    def tem_combustivel(self):
        return self.combustivel_atual > 0

    # Método sem retorno: gasta 1 unidade de combustível ao usar a magia
    def gastar_combustivel(self):
        if self.combustivel_atual > 0:
            self.combustivel_atual -= 1

    # Método com retorno: monta o texto "3/5" para mostrar no menu
    def status_combustivel(self):
        if self.tem_combustivel():
            return f"{self.combustivel_atual}/{self.combustivel_maximo}"
        return "ESGOTADO"


class Pokemon:
    """Representa um Pokemon participante da batalha."""

    def __init__(self, nome, vida_max, ataque, defesa, tipo):
        self.nome = nome
        self.vida_max = vida_max
        self.vida_atual = vida_max
        self.ataque = ataque
        self.defesa = defesa
        self.tipo = tipo
        self.golpes = []

    # Método sem retorno: adiciona uma magia à lista de golpes do Pokemon
    def aprender_golpe(self, magia):
        self.golpes.append(magia)

    # Método com retorno: True se pelo menos uma magia ainda tem combustível
    def possui_golpe_disponivel(self):
        return any(golpe.tem_combustivel() for golpe in self.golpes)

    # Método com retorno: escolhe um golpe aleatório ENTRE OS DISPONÍVEIS
    # (usado pelo computador). Retorna None se todas estiverem sem combustível.
    def escolher_golpe_aleatorio(self):
        disponiveis = [golpe for golpe in self.golpes if golpe.tem_combustivel()]
        if not disponiveis:
            return None
        return random.choice(disponiveis)

    # Método com retorno: mostra um menu numerado (com o combustível de cada
    # magia) e devolve a magia escolhida pelo jogador. Retorna None se não
    # houver nenhuma magia disponível (obriga o Ataque Desesperado).
    def escolher_golpe_jogador(self):
        print(f"\nVez de {self.nome}! Escolha uma magia:")

        if not self.possui_golpe_disponivel():
            print("  Todas as suas magias estão sem combustível!")
            return None

        for indice, golpe in enumerate(self.golpes, start=1):
            print(f"  {indice} - {golpe.nome} ({golpe.tipo}, poder {golpe.poder}) "
                  f"[combustível: {golpe.status_combustivel()}]")

        escolha = None
        while escolha is None:
            entrada = input("Digite o número da magia: ")
            if entrada.isdigit() and 1 <= int(entrada) <= len(self.golpes):
                golpe = self.golpes[int(entrada) - 1]
                if golpe.tem_combustivel():
                    escolha = golpe
                else:
                    print("Essa magia está sem combustível. Escolha outra.")
            else:
                print("Opção inválida, tente novamente.")

        return escolha

    # Método sem retorno: aplica dano recebido, sem deixar a vida ficar negativa
    def receber_dano(self, dano):
        self.vida_atual -= dano
        if self.vida_atual < 0:
            self.vida_atual = 0

    # Método com retorno: verifica se o Pokemon ainda pode batalhar
    def esta_vivo(self):
        return self.vida_atual > 0

    # Método sem retorno: exibe o status atual do Pokemon
    def mostrar_status(self):
        print(f"{self.nome} -> HP: {self.vida_atual}/{self.vida_max}")


# ---------------------------------------------------------
# "Pokedex": cada função abaixo CRIA e RETORNA um Pokemon novo,
# já com seu tipo, seus golpes e o combustível de cada um.
# ---------------------------------------------------------

def criar_charmander():
    p = Pokemon("Charmander", 100, 15, 5, tipo="Fogo")
    p.aprender_golpe(Magia("Lança-Chamas", 12, "Fogo", combustivel_maximo=5))
    p.aprender_golpe(Magia("Arranhão", 6, "Normal", combustivel_maximo=10))
    p.aprender_golpe(Magia("Garra de Fogo", 9, "Fogo", combustivel_maximo=6))
    return p


def criar_bulbasaur():
    p = Pokemon("Bulbasaur", 100, 12, 8, tipo="Planta")
    p.aprender_golpe(Magia("Chicote de Videira", 10, "Planta", combustivel_maximo=6))
    p.aprender_golpe(Magia("Investida", 7, "Normal", combustivel_maximo=10))
    p.aprender_golpe(Magia("Folha Navalha", 9, "Planta", combustivel_maximo=6))
    return p


def criar_squirtle():
    p = Pokemon("Squirtle", 105, 11, 9, tipo="Água")
    p.aprender_golpe(Magia("Jato de Água", 11, "Água", combustivel_maximo=5))
    p.aprender_golpe(Magia("Casco-Concha", 6, "Normal", combustivel_maximo=10))
    p.aprender_golpe(Magia("Investida de Água", 8, "Água", combustivel_maximo=6))
    return p


def criar_pikachu():
    p = Pokemon("Pikachu", 90, 16, 4, tipo="Elétrico")
    p.aprender_golpe(Magia("Lança-Trovão", 13, "Elétrico", combustivel_maximo=4))
    p.aprender_golpe(Magia("Choque do Rato", 6, "Elétrico", combustivel_maximo=10))
    p.aprender_golpe(Magia("Investida", 8, "Normal", combustivel_maximo=8))
    return p


# Método com retorno: monta a lista de "fábricas" de Pokemon disponíveis
def criar_pokedex():
    return [criar_charmander, criar_bulbasaur, criar_squirtle, criar_pikachu]


# Método com retorno: para um tipo dado, devolve a lista de tipos que são
# super efetivos contra ele (ou seja, as "fraquezas" desse tipo)
def listar_fraquezas(tipo):
    return [tipo_ataque for tipo_ataque, tipo_alvo in TABELA_VANTAGENS.items() if tipo_alvo == tipo]


# Método com retorno: mostra o menu da Pokedex (já com as fraquezas de cada
# um) e devolve (indice, pokemon) escolhidos pelo jogador
def escolher_pokemon_jogador(pokedex):
    print("=== Escolha o seu Pokémon ===")
    for indice, fabrica in enumerate(pokedex, start=1):
        preview = fabrica()  # cria uma cópia só para mostrar os dados no menu
        fracos_contra = listar_fraquezas(preview.tipo)
        texto_fraqueza = f" | fraco contra: {', '.join(fracos_contra)}" if fracos_contra else ""
        print(f"  {indice} - {preview.nome} ({preview.tipo}) "
              f"- HP {preview.vida_max}, ATK {preview.ataque}, DEF {preview.defesa}{texto_fraqueza}")

    escolha = None
    while escolha is None:
        entrada = input("Digite o número do Pokémon: ")
        if entrada.isdigit() and 1 <= int(entrada) <= len(pokedex):
            escolha = int(entrada)
        else:
            print("Opção inválida, tente novamente.")

    indice_escolhido = escolha - 1
    return indice_escolhido, pokedex[indice_escolhido]()


# Método com retorno: sorteia um Pokemon para o computador, evitando repetir
# a mesma espécie escolhida pelo jogador. Chamado de novo a cada partida,
# então o adversário muda a cada rodada de "jogar novamente".
def escolher_pokemon_computador(pokedex, indice_do_jogador):
    opcoes = [fabrica for i, fabrica in enumerate(pokedex) if i != indice_do_jogador]
    fabrica_sorteada = random.choice(opcoes)
    return fabrica_sorteada()


# Método com retorno: calcula o multiplicador de dano pelo tipo do golpe
# contra o tipo do defensor (1.5 se for super efetivo, 1.0 caso contrário)
def calcular_bonus_tipo(tipo_ataque, tipo_defensor):
    if TABELA_VANTAGENS.get(tipo_ataque) == tipo_defensor:
        return 1.5
    return 1.0


# Método com retorno: calcula o dano de um ataque considerando ataque, poder
# do golpe, defesa e o bônus de tipo. Devolve o dano e se foi super efetivo.
def calcular_dano(atacante, golpe, defensor):
    dano_base = (atacante.ataque + golpe.poder) - defensor.defesa
    if dano_base < 1:
        dano_base = 1  # todo golpe causa pelo menos 1 de dano

    bonus = calcular_bonus_tipo(golpe.tipo, defensor.tipo)
    dano = int(dano_base * bonus)
    dano = max(dano, 1)

    foi_super_efetivo = bonus > 1.0
    return dano, foi_super_efetivo


# Método sem retorno: executa uma rodada de ataque, usando uma magia já
# escolhida. Se golpe_escolhido for None, o Pokemon está sem combustível em
# todas as magias e usa um Ataque Desesperado (dano fixo, sem gastar
# combustível e sem bônus de tipo).
def atacar(atacante, golpe_escolhido, defensor):
    if golpe_escolhido is None:
        dano = 4
        defensor.receber_dano(dano)
        print(f"{atacante.nome} está sem combustível e usa um Ataque Desesperado, "
              f"causando {dano} de dano em {defensor.nome}!")
        return

    dano, foi_super_efetivo = calcular_dano(atacante, golpe_escolhido, defensor)
    golpe_escolhido.gastar_combustivel()
    defensor.receber_dano(dano)

    texto_efetividade = " É super efetivo!" if foi_super_efetivo else ""

    print(f"{atacante.nome} usou {golpe_escolhido.nome} ({golpe_escolhido.tipo}) "
          f"e causou {dano} de dano em {defensor.nome}!{texto_efetividade} "
          f"[combustível restante: {golpe_escolhido.status_combustivel()}]")


# Método com retorno: verifica e retorna o Pokemon vencedor (ou None se a batalha continua)
def verificar_vencedor(p1, p2):
    if not p1.esta_vivo():
        return p2
    elif not p2.esta_vivo():
        return p1
    return None


# Método sem retorno: joga uma partida completa, do sorteio do adversário até
# o anúncio do vencedor
def jogar_partida(pokedex):
    indice_jogador, jogador = escolher_pokemon_jogador(pokedex)
    computador = escolher_pokemon_computador(pokedex, indice_jogador)

    print(f"\nVocê escolheu: {jogador.nome} ({jogador.tipo})")
    print(f"Pokemon adversário: {computador.nome} ({computador.tipo})\n")

    turno = 1
    vencedor = None

    while vencedor is None:
        print(f"--- Turno {turno} ---")

        golpe_jogador = jogador.escolher_golpe_jogador()
        atacar(jogador, golpe_jogador, computador)
        vencedor = verificar_vencedor(jogador, computador)

        if vencedor is None:
            golpe_computador = computador.escolher_golpe_aleatorio()
            atacar(computador, golpe_computador, jogador)
            vencedor = verificar_vencedor(jogador, computador)

        print()
        jogador.mostrar_status()
        computador.mostrar_status()

        turno += 1

    print("\n=== Fim da Batalha! ===")
    print(f"O vencedor foi: {vencedor.nome}! 🏆")


# Método com retorno: pergunta ao jogador se ele quer jogar de novo e devolve
# True/False de acordo com a resposta
def perguntar_jogar_novamente():
    resposta = input("\nDeseja jogar novamente? (s/n): ").strip().lower()
    return resposta == "s"


def main():
    print("=== Bem-vindo à Batalha Pokemon! ===\n")

    pokedex = criar_pokedex()

    continuar_jogando = True
    while continuar_jogando:
        jogar_partida(pokedex)
        continuar_jogando = perguntar_jogar_novamente()

    print("\nValeu por jogar! Até a próxima batalha! 👋")


if __name__ == "__main__":
    main()
