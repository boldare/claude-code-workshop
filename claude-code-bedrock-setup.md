# Claude Code + AWS Bedrock - konfiguracja (Windows/PowerShell)

## Wymagane zmienne środowiskowe

```powershell
$env:CLAUDE_CODE_USE_BEDROCK = "1"
$env:AWS_REGION = "us-east-1"
```

**Uwaga:** Claude Code **nie czyta** regionu z `.aws/config` - trzeba ustawić explicite.

## AWS Credentials (jedna z opcji)

```powershell
# Opcja 1: przez profil
$env:AWS_PROFILE = "nazwa-profilu"

# Opcja 2: przez klucze
$env:AWS_ACCESS_KEY_ID = "..."
$env:AWS_SECRET_ACCESS_KEY = "..."
$env:AWS_SESSION_TOKEN = "..."  # jeśli temporary credentials

# Opcja 3: Bedrock API key
$env:AWS_BEARER_TOKEN_BEDROCK = "..."
```

## Model pinning (opcjonalne, zalecane)

```powershell
$env:ANTHROPIC_DEFAULT_OPUS_MODEL = "us.anthropic.claude-opus-4-6-v1"
$env:ANTHROPIC_DEFAULT_SONNET_MODEL = "us.anthropic.claude-sonnet-4-6"
$env:ANTHROPIC_DEFAULT_HAIKU_MODEL = "us.anthropic.claude-haiku-4-5-20251001-v1:0"
```

## Weryfikacja

```powershell
# Sprawdź czy AWS credentials działają
aws sts get-caller-identity

# Sprawdź czy zmienne są ustawione
echo $env:CLAUDE_CODE_USE_BEDROCK
echo $env:AWS_REGION

# Sprawdź dostęp do modeli Claude w Bedrock
aws bedrock list-foundation-models --region us-east-1 --query "modelSummaries[?contains(modelId, 'claude')]"

# Testuj Bedrock bezpośrednio
aws bedrock-runtime invoke-model --model-id anthropic.claude-sonnet-4-6-20250514-v1:0 --region us-east-1 --body '{"anthropic_version":"bedrock-2023-05-31","max_tokens":100,"messages":[{"role":"user","content":"hi"}]}' output.json
```

## Checklist przy błędzie 403 "Missing API key"

1. `CLAUDE_CODE_USE_BEDROCK=1` jest ustawione w **tej samej sesji** PowerShell co `claude`
2. `AWS_REGION` jest ustawiony
3. AWS credentials są skonfigurowane (profil lub klucze)
4. Model access jest włączony w AWS Console → Bedrock → Model access
5. Rola IAM ma uprawnienia `bedrock:InvokeModel` i `bedrock:InvokeModelWithResponseStream`
6. Claude Code jest aktualny: `npm update -g @anthropic-ai/claude-code`

## Alternatywa: settings file

Zamiast zmiennych środowiskowych można użyć pliku ustawień:

```json
{
  "env": {
    "CLAUDE_CODE_USE_BEDROCK": "1",
    "AWS_REGION": "us-east-1",
    "AWS_PROFILE": "nazwa-profilu",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "us.anthropic.claude-sonnet-4-6"
  }
}
```
