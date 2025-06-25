# Validador-de-Bandeiras
Desafio DIO para validação de bandeiras de cartão de crédito
Este projeto implementa um identificador de bandeiras de cartões de crédito, com validação completa usando expressões regulares e o algoritmo de **Luhn**, garantindo que o número do cartão seja válido antes da identificação da bandeira.
import re

class CartaoInvalidoException(Exception):
    pass

class BandeiraCartao:
    bandeiras = {
        "Visa": r"^4\d{12}(\d{3})?$",
        "Mastercard": r"^5[1-5]\d{14}$|^2(2[2-9][1-9]|[3-6]\d{2}|7([01]\d|20))\d{12}$",
        "Amex": r"^3[47]\d{13}$",
        "Diners Club": r"^3(0[0-5]|[68]\d)\d{11}$",
        "Discover": r"^6(011|5\d{2})\d{12}$",
        "JCB": r"^35(2[89]|[3-8]\d)\d{12}$",
        "Voyager": r"^8699\d{11}$",
        "Hipercard": r"^(606282\d{10}(\d{3})?|3841\d{15})$",
        "Aura": r"^50[0-9]{14,17}$"
    }

    @classmethod
    def identificar(cls, numero: str) -> str:
        numero = re.sub(r'\D', '', numero)
        if not numero:
            raise CartaoInvalidoException("Número do cartão vazio ou inválido.")

        if not cls.validar_luhn(numero):
            raise CartaoInvalidoException("Número do cartão inválido pelo algoritmo de Luhn.")

        for bandeira, padrao in cls.bandeiras.items():
            if re.fullmatch(padrao, numero):
                return bandeira

        raise CartaoInvalidoException("Bandeira não reconhecida para o número informado.")

    @staticmethod
    def validar_luhn(numero: str) -> bool:
        total = 0
        reverso = numero[::-1]
        for i, digito in enumerate(reverso):
            n = int(digito)
            if i % 2 == 1:
                n *= 2
                if n > 9:
                    n -= 9
            total += n
        return total % 10 == 0

# Testes
if __name__ == "__main__":
    exemplos = {
        "4111 1111 1111 1111": "Visa",
        "5500 0000 0000 0004": "Mastercard",
        "3400 0000 0000 009": "Amex",
        "3000 0000 0000 04": "Diners Club",
        "6011 0000 0000 0004": "Discover",
        "3528 0000 0000 0000": "JCB",
        "8699 0000 0000 0000": "Voyager",
        "6062 8200 0000 0000": "Hipercard",
        "5078 1234 5678 9012": "Aura",
        "1234 5678 9012 3456": None  # inválido
    }

    for numero, esperado in exemplos.items():
        try:
            resultado = BandeiraCartao.identificar(numero)
            if resultado == esperado:
                print(f"{numero} ➜ {resultado} ✅")
            else:
                print(f"{numero} ➜ {resultado} ❌ (esperado: {esperado})")
        except CartaoInvalidoException as e:
            if esperado is None:
                print(f"{numero} ➜ Rejeitado corretamente ❌ ({e})")
            else:
                print(f"{numero} ➜ Erro: {e}")
