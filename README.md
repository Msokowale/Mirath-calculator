# Mirath-calculator
A python project for calculations of Islamic inheritance
def mirath(total, spouse=None, father=False, mother=False, sons=0, daughters=0, brothers=0, sisters=0):
    """
    total: total estate
    spouse: 'husband' or 'wife' or None
    father, mother: True/False
    sons, daughters, brothers, sisters: counts
    Based on An-Nisaa: 11, 12, 176
    """
    shares = {}
    remaining = total

    # 1. SPOUSE SHARE - An-Nisaa 12
    if spouse == 'husband':
        shares['husband'] = total * (1/4 if (sons+daughters)>0 else 1/2) # 12
        remaining -= shares['husband']
    elif spouse == 'wife':
        shares['wife'] = total * (1/8 if (sons+daughters)>0 else 1/4) # 12
        remaining -= shares['wife']

    # 2. PARENTS - An-Nisaa 11
    if father and mother:
        shares['father'] = total * (1/6) # 11
        shares['mother'] = total * (1/6) # 11
        remaining -= shares['father'] + shares['mother']
    elif mother and not father:
        shares['mother'] = total * (1/6 if (sons+daughters+brothers+sisters)>0 else 1/3) # 11
        remaining -= shares['mother']
    elif father and not mother:
        shares['father'] = total * (1/6) # 11
        remaining -= shares['father']

    # 3. CHILDREN - An-Nisaa 11
    if sons > 0 or daughters > 0:
        if sons > 0 and daughters > 0:
            # "for the male, a share equal to that of two females" 11
            parts = sons*2 + daughters*1
            shares['son'] = remaining * (2/parts)
            shares['daughter'] = remaining * (1/parts)
        elif sons > 0:
            shares['son'] = remaining / sons
        elif daughters == 1:
            shares['daughter'] = remaining * (1/2) # 11
        elif daughters > 1:
            shares['daughter'] = remaining * (2/3) # 11
        remaining = 0 # children are asaba, take rest

    # 4. SIBLINGS - An-Nisaa 176 - Kalala case
    elif brothers == 0 and sisters == 0 and not father and not sons and not daughters:
        if brothers > 0 or sisters > 0:
            if brothers > 0 and sisters > 0:
                parts = brothers*2 + sisters*1
                shares['brother'] = remaining * (2/parts)
                shares['sister'] = remaining * (1/parts)
            elif sisters == 1:
                shares['sister'] = remaining * (1/2) # 176
            elif sisters > 1:
                shares['sister'] = remaining * (2/3) # 176
            elif brothers > 0:
                shares['brother'] = remaining / brothers

    return shares

# EXAMPLE
estate = 120000
result = mirath(
    total=estate,
    spouse='wife',
    father=True,
    mother=True,
    sons=2,
    daughters=1
)

print("Estate:", estate)
for k,v in result.items():
    print(f"{k}: {v:.2f}")