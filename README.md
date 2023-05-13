# Bah
import discord
from discord.ext import commands
from PIL import Image
import pytesseract

# Configuração do bot
intents = discord.Intents.default()
intents.message_content = True

bot = commands.Bot(command_prefix='/', intents=intents)

@bot.event
async def on_ready():
    print(f'Bot conectado como {bot.user.name}')

@bot.command()
async def imagem(ctx):
    await ctx.send("Envie a imagem para que eu possa ler.")

    def check(message):
        return message.author == ctx.author and message.attachments

    try:
        message = await bot.wait_for('message', check=check, timeout=30)
        attachment = message.attachments[0]
        image_path = 'imagem.jpg'
        await attachment.save(image_path)

        # Envia mensagem informando que a imagem foi recebida
        await ctx.send("Imagem recebida. Processando...")

        image = Image.open(image_path)
        texto_extraido = pytesseract.image_to_string(image)

        await ctx.send(f"Texto extraído da imagem:\n{texto_extraido}")

    except TimeoutError:
        await ctx.send("Tempo limite excedido. Tente novamente.")

@bot.command()
async def texto(ctx):
    # Verifica se há texto extraído da imagem
    if "texto_extraido" in globals():
        await ctx.send(f"Texto extraído da imagem:\n{texto_extraido}")
    else:
        await ctx.send("Nenhum texto foi extraído da imagem. Envie uma imagem primeiro usando o comando /imagem.")


bot.run('MTEwNjA1MDM2MzI1MDk2NjU2MA.Ga2Pg_.gz7l_zAS4uEASjMH3WV9_bqBmkj43koF0Ac6fY')
