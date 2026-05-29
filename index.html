const DEFAULT_SYSTEM = `Você é o Assistente Fluxo, agente de produtividade do Lucas.
Contexto: Lucas administra escola, consultoria/investimentos, estudos de certificação e saúde pessoal.
Sua função é analisar tarefas, objetivos, score IA, duração, urgência, impacto financeiro e carga cognitiva.
Responda em português do Brasil, direto, prático, sem tom robótico e sem economês desnecessário.
Use no máximo 5 linhas, salvo se o usuário pedir uma análise maior.
Não invente tarefas nem dados que não estejam no contexto. Quando faltar informação, diga o que falta e sugira o próximo passo.
Priorize: alto score, alto impacto financeiro, alto custo de atraso, tarefas que desbloqueiam outras e tarefas compatíveis com o tempo/energia informado.
Evite a estrutura retórica "não é X, é Y".`;

function parseBody(req){
  if(!req.body) return {};
  if(typeof req.body === 'string'){
    try{return JSON.parse(req.body)}catch(_e){return {}}
  }
  return req.body;
}

function cleanHistory(history){
  if(!Array.isArray(history)) return [];
  return history.slice(-10).filter(m=>m && (m.role==='user'||m.role==='assistant') && typeof m.content==='string')
    .map(m=>({role:m.role, content:m.content.slice(0,1200)}));
}

function buildUserPrompt(message, context){
  return `Pergunta do usuário:\n${message}\n\nContexto atual do app Fluxo em JSON:\n${JSON.stringify(context||{}, null, 2)}`;
}

async function callAnthropic({message, history, context}){
  const apiKey=process.env.ANTHROPIC_API_KEY;
  if(!apiKey) return null;
  const model=process.env.ANTHROPIC_MODEL || 'claude-sonnet-4-20250514';
  const messages=[...cleanHistory(history), {role:'user', content:buildUserPrompt(message, context)}];
  const r=await fetch('https://api.anthropic.com/v1/messages',{
    method:'POST',
    headers:{
      'content-type':'application/json',
      'x-api-key':apiKey,
      'anthropic-version':'2023-06-01'
    },
    body:JSON.stringify({
      model,
      max_tokens:450,
      system:DEFAULT_SYSTEM,
      messages
    })
  });
  const data=await r.json().catch(()=>({}));
  if(!r.ok){
    const msg=data?.error?.message || data?.message || `Anthropic HTTP ${r.status}`;
    throw new Error(msg);
  }
  const reply=(data.content||[]).map(b=>b.text||'').join('\n').trim();
  return {reply, provider:'anthropic', model};
}

async function callOpenAI({message, history, context}){
  const apiKey=process.env.OPENAI_API_KEY;
  if(!apiKey) return null;
  const model=process.env.OPENAI_MODEL || 'gpt-5.5';
  const prior=cleanHistory(history).map(m=>`${m.role==='user'?'Usuário':'Assistente'}: ${m.content}`).join('\n');
  const input=`${prior ? `Histórico recente:\n${prior}\n\n` : ''}${buildUserPrompt(message, context)}`;
  const r=await fetch('https://api.openai.com/v1/responses',{
    method:'POST',
    headers:{
      'content-type':'application/json',
      'authorization':`Bearer ${apiKey}`
    },
    body:JSON.stringify({
      model,
      instructions:DEFAULT_SYSTEM,
      input,
      max_output_tokens:450
    })
  });
  const data=await r.json().catch(()=>({}));
  if(!r.ok){
    const msg=data?.error?.message || data?.message || `OpenAI HTTP ${r.status}`;
    throw new Error(msg);
  }
  const reply=(data.output_text || (data.output||[]).flatMap(o=>o.content||[]).map(c=>c.text||'').join('\n')).trim();
  return {reply, provider:'openai', model};
}

module.exports = async function handler(req, res){
  if(req.method !== 'POST'){
    res.setHeader('Allow','POST');
    return res.status(405).json({error:'Método não permitido.'});
  }
  try{
    const body=parseBody(req);
    const message=String(body.message||'').trim();
    if(!message) return res.status(400).json({error:'Mensagem vazia.'});

    let result=null;
    // Mantém compatibilidade com a intenção original do código: Claude/Anthropic primeiro, OpenAI como fallback.
    result=await callAnthropic({message, history:body.history, context:body.context});
    if(!result) result=await callOpenAI({message, history:body.history, context:body.context});

    if(!result){
      return res.status(500).json({error:'Configure ANTHROPIC_API_KEY ou OPENAI_API_KEY nas variáveis de ambiente do Vercel.'});
    }
    return res.status(200).json(result);
  }catch(e){
    console.error('fluxo-ai error', e);
    return res.status(500).json({error:e.message||'Erro interno no agente.'});
  }
};
