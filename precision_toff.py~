"""
Geohash Length-Precision tradeoff
"""
import simpleplot
import math

def convert_to_binary_tree(axis, diff = 90.0):
    """
    Takes an axis and optional diff, indicating longitude
    or latitude, Returns a corresponding binary tree string
    """
    # Initiaze a temp_axis
    temp_axis = 0.0
    # Initiliaze other local variables
    mid = 0.0
    high = diff
    low = -diff
    # Initialize string to be returned
    bin_str = ""
    # Run loop until temp_axis != given axis
    while round(float(temp_axis), 3) != round(float(axis), 3):
        # Implement binary search to create the tree string
        if axis > mid:
            bin_str += "1"
            low = mid
            mid = (low + high) / 2
            temp_axis = mid
        else:
            bin_str += "0"
            high = mid = temp_axis
            mid = (low + high) / 2
            temp_axis = mid
    return bin_str

def pad(binary, length):
    """
    Takes a binary string and length.
    Returns padded string of given length
    """
    str_len = len(binary)
    # Till the given length
    while str_len != length:
        # Pad with 0
        binary += "0"
        str_len = len(binary)
    # Retrun padded string
    return binary

def split_binary(binary):
    """
    Takes a binary string, Returns a list with 5 bits an element
    """
    # Starting index
    start = 0
    # End index of each split
    end = start + 5
    splits = len(binary) / 5
    list_of_strings = []
    # Add element as a split
    for dumy_num in range(splits):
        list_of_strings.append(binary[start:end])
        start = end
        end = start + 5
    # Add residue
    if end != len(binary):
        list_of_strings.append(binary[start:])
    return list_of_strings

def mix_axis(lat, lngt):
    """
    Mixes latitude and logitude in binary form
    """
    # Initialize mixed string
    mixed_string = ""
    # Get lat_len
    lat_len = len(lat)
    # Get lngt_len
    lngt_len = len(lngt)
    # Iterate till the longgest one
    if lngt_len > lat_len:
        # If lngt len is larger
        lat = pad(lat, lngt_len)
        for dummy_num in range(lngt_len):
            # Mix lgnt and lat bits alternately
            # 1st bit is longitude and succeding even bits from left
            mixed_string += lngt[dummy_num]
            # Odd bits are latitude
            mixed_string += lat[dummy_num]
    else:
        # If lat_len is larger
        lngt = pad(lngt, lat_len)
        for dummy_num in range(lat_len):
            # Mix lgnt and lat bits alternately
            # 1st bit is longitude and succeding even bits from left
            mixed_string += lngt[dummy_num]
            # Odd bits are latitude
            mixed_string += lat[dummy_num]
    # Returns resulting string
    return mixed_string

def binary_to_decimal(binary):
    """
    Takes binary string, Returns corresponding decimal number
    """
    decimal = 0
    exponent = 0
    for dummy_num in range(len(binary) - 1, -1, -1):
        # Get decimal for a corresponding bit
        decimal += int(binary[dummy_num]) * (2 ** exponent)
        # Increment exponent for each bit shift
        exponent += 1
    return decimal

def create_dec_list(binary):
    """
    Takes a binary string, splits in 5 bits,
    Returns a decimal list
    """
    # Split into string of 5 bits
    base_32_list = split_binary(binary)
    dec_list = []
    for dummy_element in base_32_list:
        # Convert each bin to dec and append to list
        dec_list.append(binary_to_decimal(dummy_element))
    return dec_list

def encode_geohash(dec_list):
    """
    Takes a decimal list, Returns corresponding geohash
    """
    # Encoding dictionary
    BASE_32_DICT = {
                    0: "0",
                    1: "1",
                    2: "2",
                    3: "3",
                    4: "4",
                    5: "5",
                    6: "6",
                    7: "7",
                    8: "8",
                    9: "9",
                    10: "b",
                    11: "c",
                    12: "d",
                    13: "e",
                    14: "f",
                    15: "g",
                    16: "h",
                    17: "j",
                    18: "k",
                    19: "m",
                    20: "n",
                    21: "p",
                    22: "q",
                    23: "r",
                    24: "s",
                    25: "t",
                    26: "u",
                    27: "v",
                    28: "w",
                    29: "x",
                    30: "y",
                    31: "z"}
    # Initialize empty geohash
    geohash = ""
    for dummy_num in dec_list:
        # Encode each dec to hash and append to geohash
        geohash += BASE_32_DICT[dummy_num]
    return geohash

def bin_tree_geohash():
    """ 
    Takes user input, converts into bst_geohash
    """
    # Input latitude
    usr_lat = float(raw_input("Enter latitude: "))
    # Input longitude
    usr_lngt = float(raw_input("Enter longitude: "))
    # Convert latitude in bits
    lat_bits = convert_to_binary_tree(usr_lat)
    # Convert longitude in bits
    lngt_bits = convert_to_binary_tree(usr_lngt, 180.0)
    # Mix latitude and longitude bits
    mixed_bits = mix_axis(lat_bits, lngt_bits)
    # Get the corresponding decimal list
    dec_list = create_dec_list(mixed_bits)
    # Encode decimals to geohash
    geohash = encode_geohash(dec_list)
    return geohash

def get_precision(geohash_length):
    """
    Takes a geohash_length,
    Returns a circular area of probable position
    """
    # Define precision list upto 8
    precision_list = [6378.1, 2500, 630, 78, 20, 2.4, 0.61, 0.076, 0.019]
    return precision_list[geohash_length]

def decrease_hash(geohash):
    """
    Takes a hash, Returns with 1 less lsd
    """
    return geohash[:-2]

def generate_dataset(geohash):
    """
    Takes a hash value,
    Generates dataset(list) of length-precision tuples
    """
    dataset = []
    geo_len = len(geohash)
    for dummy_num in range(geo_len):
        # Generate each data decreasing geohash length by 1
        data = (geo_len, circular_area(get_precision(geo_len)))
        dataset.append(data)
        geohash = decrease_hash(geohash)
        geo_len = len(geohash)
    return dataset    

def circular_area(radius):
    """
    Returns area from given radius
    """
    return math.pi * (radius ** 2)

dataset = generate_dataset(bin_tree_geohash())

simpleplot.plot_lines('GeoHash Precision Tradeoff', 400, 300, "Length", "Error(km^2)", [dataset], True)        
