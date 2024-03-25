# ollama
it is how we can get ollama run over google.colab


!pip install aiohttp pyngrok

import os
import asyncio

# Set LD_LIBRARY_PATH so the system NVIDIA library 
os.environ.update({'LD_LIBRARY_PATH': '/usr/lib64-nvidia'})

async def run_process(cmd):
  print('>>> starting', *cmd)
  p = await asyncio.subprocess.create_subprocess_exec(
      *cmd,
      stdout=asyncio.subprocess.PIPE,
      stderr=asyncio.subprocess.PIPE,
  )

  async def pipe(lines):
    async for line in lines:
      print(line.strip().decode('utf-8'))

  await asyncio.gather(
      pipe(p.stdout),
      pipe(p.stderr),
  )

#register an account at ngrok.com and create an authtoken and place it here
await asyncio.gather(
    run_process(['ngrok', 'config', 'add-authtoken','2eCDpQ9GsBI7TEL95Y7iSnlglPu_5x7aB9LDe2cQGRsqTvaWr'])
)

await asyncio.gather(
    run_process(['ollama', 'serve']),
    run_process(['ngrok', 'http', '--log', 'stderr', '11434', '--host-header="localhost:11434"']),
)
