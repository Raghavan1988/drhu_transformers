import re
from collections import defaultdict

def get_stats(vocab):
    pairs = defaultdict(int)
    for word, freq in vocab.items():
        symbols = word.split()
        for i in range(len(symbols) - 1):
            pairs[symbols[i], symbols[i + 1]] += freq
    return pairs

def merge_vocab(pair, vocab):
    new_vocab = {}
    bigram = re.escape(' '.join(pair))
    replacement = ''.join(pair)
    pattern = re.compile(r'(?<!\S)' + bigram + r'(?!\S)')
    for word in vocab:
        new_word = pattern.sub(replacement, word)
        new_vocab[new_word] = vocab[word]
    return new_vocab

def train_bpe(data, num_merges=10):
    vocab = {}
    for word, freq in data.items():
        word = ' '.join(list(word)) + ' </w>'
        if word in vocab:
            vocab[word] += freq
        else:
            vocab[word] = freq

    for i in range(num_merges):
        pairs = get_stats(vocab)
        if not pairs:
            break
        best_pair = max(pairs, key=pairs.get)
        vocab = merge_vocab(best_pair, vocab)
    return vocab


data = {}
with open('pg16457.txt', 'r') as f:
    for line in f:
        line = line.strip()
        if not line:
            continue
        for word in line.split():
            data[word] = data.get(word, 0) + 1

def encode_bpe(sentence, bpe_vocab):
    # Convert the sentence to its character-level representation, adding the special token </w>
    tokens = [' '.join(list(word)) + ' </w>' for word in sentence.split()]
    encoded_tokens = []
    
    # For each token in our sentence
    for token in tokens:
        while True:
            # Initialize a flag to check if we have done any replacements in this iteration
            has_replacement = False

            # For each merged pair in our BPE vocabulary
            for merged in bpe_vocab:
                # If our current token contains this merged pair
                if merged in token:
                    # Replace the first occurrence of this pair in the token
                    token = token.replace(merged, merged.replace(' ', ''), 1)
                    has_replacement = True
                    break

            # If we haven't done any replacements, exit the loop
            if not has_replacement:
                break
        
        encoded_tokens.append(token)
    
    # Join our encoded tokens to get the final encoded sentence
    return ' '.join(encoded_tokens)


bpe_vocab = train_bpe(data, num_merges=10)
sentence = "I like swimming as much as reading that book"
encoded_sentence = encode_bpe(sentence, bpe_vocab)
print(encoded_sentence)


I</w> l i k e </w> s w i m m i n g </w> a s </w> much</w> a s </w> r e a d i n g </w> t h a t </w> book</w>
