<template>
  <el-dialog v-model="props.visible" title="New rule" @close="handleClose">
    <el-form ref="formRef" status-icon :rules="rules" :model="form" label-width="120px" size="default">
      <el-form-item label="Rule name" prop="ruleName">
        <el-input v-model="form.ruleName" />
      </el-form-item>
      <el-form-item label="Promo Type" prop="type">
        <el-radio-group v-model="form.type">
          <el-radio label="Coupon" />
          <el-radio label="Deal" />
        </el-radio-group>
      </el-form-item>
      <el-form-item label="Category" prop="operationCate">
        <!-- <el-input v-model="form.operationCate" /> -->
        <div>
          <TreeSelect ref="refTreeSelect" v-model="form.operationCate" :tree-data="state.treeData" :default-props="state.defaultProps" @changeCategory="changeCategory"></TreeSelect>
        </div>
      </el-form-item>
      <el-form-item label="Description" prop="description">
        <el-input v-model="form.description" type="textarea" />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submit(formRef)">Submit</el-button>
        <el-button @click="resetForm(formRef)">Reset</el-button>
      </el-form-item>
    </el-form>
  </el-dialog>
</template>

<script lang="ts" setup>
import TreeSelect from './TreeSelect.vue'
import { ref, reactive, unref } from 'vue'
import { ElMessage, FormInstance, FormRules } from 'element-plus'

const refTreeSelect = ref()
const props = defineProps(['visible'])
const emit = defineEmits(['changeVisible', 'init'])

const handleClose = function () {
  emit('changeVisible')
}

const form = reactive({
  ruleName: '',
  type: '',
  operationCate: '',
  description: '',
})

const formRef = ref<FormInstance>()

const rules = reactive<FormRules>({
  ruleName: [{ required: true, message: 'Please input name', trigger: 'blur' }],
  type: [{ required: true, message: 'Please select type', trigger: 'blur' }],
  operationCate: [{ required: true, message: 'Please select category', trigger: 'blur' }],
})

const submit = async (formEl: FormInstance | undefined) => {
  console.log('formEL: ', formEl)
  if (!formEl) return
  await formEl.validate((valid, fields) => {
    if (valid) {
      const temp = unref(form)
      console.log('temp: ', temp)
      console.log(typeof temp)
      fetch('/api/rule', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(temp),
      })
        .then((res) => {
          if (res.status === 200) {
            ElMessage({
              message: '???????????????',
              type: 'success',
            })
            emit('init')
          } else {
            ElMessage({
              message: '????????????',
              type: 'warning',
            })
          }
        })
        .catch((err) => {
          ElMessage({
            message: '???????????????: ' + err,
            type: 'error',
          })
        })
    } else {
      console.log('error submit!', fields)
    }
  })
}

const resetForm = (formEl: FormInstance | undefined) => {
  if (!formEl) return
  refTreeSelect.value.handleNodeClick({ label: '' })
  formEl.resetFields()
}

// ????????????
interface Tree {
  label: string
  children?: Tree[]
}

const changeCategory = function (value: string) {
  form.operationCate = value
}

const state = reactive({
  defaultProps: {
    children: 'children',
    label: 'label',
  },
  treeData: [
    {
      label: 'Apparel',
      children: [
        {
          label: 'Accessories',
          children: [
            {
              label: 'Hair Accessories',
            },
            {
              label: 'Scarves',
            },
            {
              label: 'Bags & Wallets',
            },
            {
              label: 'Ties & Pocket Squares',
            },
            {
              label: 'Watches',
            },
            {
              label: 'Jewelry',
            },
            {
              label: 'Baby Accessories',
            },
            {
              label: "Women's Accessories",
            },
            {
              label: "Kids' Accessories",
            },
            {
              label: 'Sunglasses & Eyewear',
            },
            {
              label: 'Gloves',
            },
            {
              label: 'Cuff Links & Tie Clips',
            },
            {
              label: 'Belts',
            },
            {
              label: 'Hats',
            },
          ],
        },
        {
          label: 'Speciality & Boutique Stores',
          children: [
            { label: 'Novelty & Fandom' },
            { label: 'Leather Clothing' },
            { label: 'Sustainable Cloth' },
            { label: 'Ethnic Clothing' },
            { label: 'Cashmere Clothing' },
            { label: 'Western Clothing' },
          ],
        },
        {
          label: 'Fashion Retailers',
          children: [{ label: 'Discount Clothing' }, { label: 'Wholesale Clothing' }, { label: 'Fashion Brands' }],
        },
        { label: 'Department Stores' },
        {
          label: 'Clothes',
          children: [
            { label: 'Leggings' },
            { label: 'Sleepwear' },
            { label: "Men's Clothing" },
            { label: 'Designer & Luxury Fashion' },
            { label: 'Swimwear' },
            { label: 'Wedding Attire' },
            { label: 'Activewear' },
            { label: 'Coats & Jackets' },
            { label: 'Blazers' },
            { label: 'Skirts' },
            { label: 'Shorts' },
            { label: 'Underwear & Socks' },
            { label: "Kids' Apparel" },
            { label: 'Sweatshirts & Hoodies' },
            { label: "Women's Clothing" },
            { label: 'Baby Clothing' },
            { label: 'Jeans' },
            { label: 'Pants' },
            { label: 'Adventure Wear' },
            { label: 'Dresses' },
            { label: 'Tops & Shirts' },
            { label: 'Sportswear' },
            { label: 'Suits & Separates' },
            { label: 'Maternity Clothing' },
            { label: 'Sweaters' },
          ],
        },
        {
          label: 'Footwear',
          children: [
            { label: 'Sneakers' },
            { label: 'Comfort Shoes' },
            { label: "Men's Shoes" },
            { label: 'Oxfords' },
            { label: 'Shoe Care & Accessories' },
            { label: "Kids' Shoes" },
            { label: "Women's Shoes" },
            { label: 'Boots' },
            { label: 'Slippers' },
            { label: 'Rock Climbing Shoes' },
            { label: 'Dress Shoes' },
            { label: 'Sandals & Flipflops' },
            { label: 'Golf Footwear' },
          ],
        },
        {
          label: 'Styling Subscriptions',
        },
        {
          label: 'Workwear',
          children: [{ label: 'Military & Police Gear' }, { label: 'Uniforms & Tactical Gear' }, { label: 'Firefighter Gear' }, { label: 'Scrubs & Medical Uniforms' }, { label: 'Job Uniforms' }],
        },
      ],
    },
    {
      label: 'Beauty',
      children: [
        {
          label: 'Fragrances',
          children: [{ label: 'Fragrances for Children' }, { label: 'Unisex Fragrances' }, { label: 'Fragrances for Women' }, { label: 'Fragrances for Men' }],
        },
        {
          label: 'Shaving & Hair Removal',
          children: [{ label: 'Laser Hair Removal' }, { label: 'Grooming Kits' }, { label: 'Clippers & Trimmers' }, { label: 'Shaving Creams' }, { label: 'Shavers' }, { label: 'Hair Waxing' }],
        },
        {
          label: 'Nail Care',
          children: [{ label: 'Nail Treatments' }, { label: 'Nail Polish' }, { label: 'Manicure & Pedicure Tools' }, { label: 'Nail Art' }],
        },
        {
          label: 'Hair Care',
          children: [
            { label: 'Hair Color' },
            { label: 'Hair Salons' },
            { label: 'Extensions & Wigs' },
            { label: 'Hair Loss' },
            { label: 'Shampoo & Conditioner' },
            { label: 'Hair Treatments' },
            { label: 'Hair Styling' },
          ],
        },
        {
          label: 'Skin Care',
          children: [{ label: 'Bath & Body' }, { label: 'Hand & Foot Care' }, { label: 'Lip Care' }, { label: 'Face Care' }, { label: 'Anti-Aging Products' }, { label: 'Eye Care' }],
        },
        {
          label: 'Makeup',
          children: [{ label: 'Makeup Brushes' }, { label: 'Eye Makeup' }, { label: 'Body Makeup' }, { label: 'Face Makeup' }, { label: 'Makeup Accessories & Organizers' }, { label: 'Lip Makeup' }],
        },
        {
          label: 'Oral Care',
          children: [{ label: 'Teeth Whitening' }, { label: 'Toothpaste' }, { label: 'Mouthwash' }, { label: 'Teeth Correction' }, { label: 'Dental Flossers' }, { label: 'Toothbrushes' }],
        },
        {
          label: 'Self-care Subscriptions',
        },
      ],
    },
    {
      label: 'Electronics',
      children: [
        {
          label: 'TVs & Home Theater',
          children: [{ label: 'TVs' }, { label: 'Video Players' }, { label: 'TV Audio' }, { label: 'TV & Home Theater Accessories' }],
        },
        {
          label: 'Computers & Accessories',
          children: [
            { label: 'Typewriters' },
            { label: 'Gaming Computers' },
            { label: 'Computer Accessories' },
            { label: 'Tablets & Accessories' },
            { label: 'Monitors' },
            { label: 'Laptops' },
            { label: 'Desktops' },
            { label: 'Printers' },
            { label: 'PC Components' },
          ],
        },
        {
          label: 'Smart Home',
          children: [
            { label: 'Smart Plugs & Outlets' },
            { label: 'Smart Entry' },
            { label: 'Smart Climate Control' },
            { label: 'Smart Pet Devices' },
            { label: 'Security Cameras & Systems' },
            { label: 'Smart Lawn & Garden' },
            { label: 'Voice Assistants & Hubs' },
            { label: 'Smart Lighting' },
            { label: 'Smart Bathroom' },
            { label: 'Smart Kitchen' },
            { label: 'Smart Detectors & Sensors' },
          ],
        },
        {
          label: 'Speakers & Audio',
          children: [{ label: 'Headphones' }, { label: 'Docks' }, { label: 'Speakers' }],
        },
        {
          label: 'Video Games & Accessories',
          children: [{ label: 'Gaming Consoles & Systems' }, { label: 'Video Game Accessories' }],
        },
        {
          label: 'Cell Phones & Accessories',
          children: [{ label: 'Cell Phone Retailers' }, { label: 'Cell Phone Repair' }, { label: 'Cell Phone Carriers' }, { label: 'Cell Phones' }, { label: 'Cell Phone Accessories' }],
        },
        {
          label: 'Wearable Technology',
          children: [
            { label: 'Fitness Trackers' },
            { label: 'Wellness Wearables' },
            { label: 'Virtual Reality Headsets & Accessories' },
            { label: 'Smart Glasses' },
            { label: 'Smart Rings' },
            { label: 'Smartwatches' },
          ],
        },
        {
          label: 'Car Electronics & GPS',
          children: [{ label: 'Car Security & Safety' }, { label: 'Car Video' }, { label: 'Car Audio' }, { label: 'GPS Trackers' }],
        },
        {
          label: 'WiFi & Networking ',
          children: [{ label: 'WiFi Hotspots' }, { label: 'Modems' }, { label: 'Wireless Access Points ' }, { label: 'WiFi Networking Accessories' }, { label: 'Mesh WiFi Systems' }],
        },
        {
          label: 'Cameras & Camcorders',
          children: [
            { label: 'Video Cameras' },
            { label: 'Digital Cameras' },
            { label: 'Camera Accessories' },
            { label: 'Professional Film Equipment' },
            { label: 'Film Cameras' },
            { label: 'Camera Retailers' },
          ],
        },
      ],
    },
    {
      label: 'Health',
      children: [
        {
          label: 'Medicine Cabinet',
          children: [
            { label: 'Cannabis & Smoking' },
            { label: 'Online Pharmacy' },
            { label: 'Medication Dispensers' },
            { label: 'Cough & Cold' },
            { label: 'Allergy & Sinus' },
            { label: 'Pain Relief' },
            { label: 'Natural & Alternative Remedies' },
            { label: 'Digestion & Nausea' },
          ],
        },
        {
          label: 'Vision Care',
          children: [{ label: 'Eyeglass Accessories' }, { label: 'Contact Lenses & Care' }, { label: 'Reading Glasses' }, { label: 'Eyeglasses & Frames' }, { label: 'Eye Drops & Washes' }],
        },
        {
          label: 'Wellness & Health Care',
          children: [
            { label: 'Addiction Treatments' },
            { label: 'Feminine Care' },
            { label: 'Wellness Apps & Services' },
            { label: 'Adult Incontinence' },
            { label: 'Sexual Wellness & Family Planning' },
            { label: 'Diabetes Care' },
            { label: 'Massages & Spa' },
            { label: 'Home Diagnostic Tests' },
            { label: 'Sleep Aids' },
            { label: 'Smoking Cessation' },
          ],
        },
        {
          label: 'Fitness & Exercise',
          children: [
            { label: 'Cardio Equipment' },
            { label: 'Fitness Equipment Parts' },
            { label: 'Exercise Accessories' },
            { label: 'Yoga' },
            { label: 'Fitness Subscription Boxes' },
            { label: 'Sports & Fitness Apps' },
            { label: 'Fitness Memberships & Classes' },
            { label: 'Strength Training' },
            { label: 'Recovery & Prevention Products' },
          ],
        },
        {
          label: 'Vitamins & Supplements',
          children: [
            { label: 'Vitamin Subscriptions' },
            { label: 'Health & Nutrition Retailers' },
            { label: 'Sports Nutrition' },
            { label: 'Herbal & Natural Supplements' },
            { label: 'Nutritional Supplements' },
            { label: 'Vitamins & Minerals' },
            { label: 'Dietary Supplements' },
            { label: 'Endurance & Energy Supplements' },
          ],
        },
        {
          label: 'Medical Supplies & Equipment',
          children: [
            { label: 'Compression Socks' },
            { label: 'Braces' },
            { label: 'Protective Face Masks' },
            { label: 'Mobility & Daily Living Aids' },
            { label: 'First Aid' },
            { label: 'Health Monitors' },
            { label: 'Lifting Cushions' },
          ],
        },
        {
          label: 'Sports & Outdoors',
          children: [
            { label: 'Cycling' },
            { label: 'Hunting' },
            { label: 'Golfing' },
            { label: 'Climbing' },
            { label: 'Winter Sports' },
            { label: 'Sporting Goods' },
            { label: 'Camping & Hiking' },
            { label: 'Skates' },
            { label: 'Leisure Sports & Game Room' },
            { label: 'Sports Fan Gear' },
            { label: 'Boating & Fishing' },
            { label: 'Water Sports' },
            { label: 'Team Sports' },
          ],
        },
      ],
    },
    {
      label: 'Home',
      children: [
        {
          label: 'Hobbies & Toys',
          children: [
            { label: 'Party Decorating & Supplies' },
            { label: 'Toys' },
            { label: 'Dress Up & Pretend Play' },
            { label: 'Gifts' },
            { label: 'Musical Instruments & Merchandise' },
            { label: 'Play Vehicles' },
            { label: 'Games & Accessories' },
            { label: 'Hobbies' },
            { label: 'Collectibles' },
            { label: 'Personal Affiliation ' },
            { label: 'Arts' },
          ],
        },
        {
          label: 'Garden & Outdoor',
          children: [
            { label: 'Outdoor Heating' },
            { label: 'Yard Power Equipment' },
            { label: 'Outdoor Cooking' },
            { label: 'Gardening' },
            { label: 'Snow Removal' },
            { label: 'Generators' },
            { label: 'Pools' },
            { label: 'Outdoor Decor ' },
          ],
        },
        {
          label: 'Pets',
          children: [
            { label: 'Pet Food Subscriptions' },
            { label: 'Bird Care' },
            { label: 'Horse Care' },
            { label: 'Pet Supplies' },
            { label: 'Reptiles & Amphibian Care' },
            { label: 'Cat Care' },
            { label: 'Dog Care' },
            { label: 'Pet Services' },
            { label: 'Fish & Aquatic Pet Care' },
          ],
        },
        {
          label: 'Grocery & Food',
          children: [
            { label: 'Food & Ingredients' },
            { label: 'Grocery Stores' },
            { label: 'Fresh Flowers & Live Indoor' },
            { label: 'Food Delivery & Takeout' },
            { label: 'Food & Drink Subscriptions' },
            { label: 'Cooking Classes' },
            { label: 'Beverages' },
            { label: 'Recipe Resources' },
          ],
        },
        {
          label: 'Home Improvement',
          children: [
            { label: 'Plumbing' },
            { label: 'Metal Fabrication' },
            { label: 'Household Cleaning' },
            { label: 'Safety & Security Equipment' },
            { label: 'Home Services' },
            { label: 'Tools & Machinery' },
            { label: 'Household Electronic Parts ' },
            { label: 'Electrical & Lighting' },
            { label: 'Kitchen & Bathroom Fixtures' },
            { label: 'Home Painting & Wall Treatments' },
            { label: 'Heating & Cooling' },
            { label: 'Decks & Flooring' },
            { label: 'Renewable Energy' },
            { label: 'Laundry' },
            { label: 'Woodworking' },
            { label: 'Trash & Recycling' },
          ],
        },
        {
          label: 'Home Decor',
          children: [
            { label: 'Table Accessories' },
            { label: 'Rugs' },
            { label: 'Home Fragrances' },
            { label: 'Window Treatments' },
            { label: 'Seasonal Decor' },
            { label: 'Interior Designers' },
            { label: 'Throw Pillows & Covers' },
            { label: 'Bean Bags' },
            { label: 'Wall Decorations' },
            { label: 'Slipcovers' },
            { label: 'Mirrors' },
            { label: 'Home Decor Retailers' },
            { label: 'Clocks' },
          ],
        },
        {
          label: 'Baby',
          children: [
            { label: 'Baby Saftey' },
            { label: 'Baby Feeding' },
            { label: 'Strollers & Accessories' },
            { label: 'Babysitting Services' },
            { label: 'Baby Activity & Entertainment' },
            { label: 'Baby Education' },
            { label: 'Baby & Toddler Toys' },
            { label: 'Diapering' },
            { label: 'Car Seats & Accessories' },
            { label: 'Nursery' },
            { label: 'Potty Training' },
          ],
        },
        {
          label: 'Bed & Bath',
          children: [
            { label: 'Pillows & Pillowcases' },
            { label: 'Blankets & Throws' },
            { label: "Kids' Bedding" },
            { label: 'Bathroom Accessories' },
            { label: 'Bathroom Linens' },
            { label: 'Bedroom Storage' },
            { label: 'Basic Bedding' },
            { label: 'Mattresses & Bedding' },
            { label: 'Bathroom Storage' },
            { label: 'Bedding Sets & Collections' },
          ],
        },
        {
          label: 'Furniture',
          children: [
            { label: 'Bedroom Furniture' },
            { label: 'Furniture Rental' },
            { label: "Kids' Furniture" },
            { label: 'Living Room Furniture' },
            { label: 'Furniture Retailers' },
            { label: 'Patio Furniture' },
            { label: 'Kitchen & Dining Furniture' },
            { label: 'Home Entertainment Furniture' },
            { label: 'Office Furniture' },
          ],
        },
        {
          label: 'Automotive',
          children: [
            { label: 'Auto Performance Parts & Accessories' },
            { label: 'Motorcycles & Powersports' },
            { label: 'Auto Interior Accessories' },
            { label: 'Auto Exterior Accessories' },
            { label: 'Car Care' },
            { label: 'Auto Dealers' },
            { label: 'Auto Replacement Parts' },
            { label: 'Auto Resources & Services' },
            { label: 'RVs & Motorhomes' },
            { label: 'Automotive Fan Gear' },
            { label: 'Auto Oils & Fluids' },
            { label: 'Auto Tools & Equipment' },
            { label: 'Truck Parts & Accessories' },
            { label: 'Tires & Wheels' },
          ],
        },
        {
          label: 'Kitchen & Dining',
          children: [{ label: 'Kitchen Storage & Organization' }, { label: 'Cookware' }, { label: 'Dining & Entertaining' }, { label: 'Kitchen Appliances' }, { label: 'Kitchen Utensils & Gadgets' }],
        },
      ],
    },
    {
      label: 'Money',
      children: [
        {
          label: 'Commercial & Industrial',
          chilren: [
            { label: 'Spa & Massage Supplies' },
            { label: 'Hydraulics' },
            { label: 'Professional Dental Supplies' },
            { label: 'Lab & Scientific Products' },
            { label: 'Agricultural Irrigation Equipment' },
            { label: 'Packaging & Shipping' },
            { label: 'Material Handling' },
            { label: 'Administrative Services' },
            { label: 'Retail Fixtures & Supplies' },
            { label: 'Industrial Electrical' },
            { label: 'Food Service Equipment & Supplies' },
            { label: 'Industrial Hardware' },
            { label: 'Professional Medical Supplies' },
            { label: 'Robotics Supplies' },
          ],
        },
        {
          label: 'People Search',
          chilren: [
            {
              label: 'Background Checks',
            },
          ],
        },
        {
          label: 'Financial Advising',
          chilren: [
            { label: 'Financial Consultants' },
            { label: 'Portfolio & Wealth Management' },
            { label: 'Trading & Brokerage Services' },
            { label: 'Certified Financial Planners' },
            { label: 'Investment Advisors' },
          ],
        },
        {
          label: 'Office & School Supplies',
          chilren: [
            { label: 'Office Machinery' },
            { label: 'Office Organization & Storage' },
            { label: 'Money Handling' },
            { label: 'Paper' },
            { label: 'School Supplies' },
            { label: 'Office Stamping' },
            { label: 'Forms & Recordkeeping' },
            { label: 'Labeling & Accessories' },
          ],
        },
        {
          label: 'Charity & Fundraising',
          chilren: [{ label: 'Charities' }, { label: 'Crowdfunding' }, { label: 'Non-Profit Organizations' }, { label: 'Fundraising Services' }],
        },
        {
          label: 'Personal Finance',
          chilren: [
            { label: 'Identity Theft Protection' },
            { label: 'Retirement Planning' },
            { label: 'Banking & Loans' },
            { label: 'DeFi' },
            { label: 'Insurance' },
            { label: 'Personal Investing & Trading' },
            { label: 'Personal Tax & Accounting' },
            { label: 'Money Management Apps' },
            { label: 'Credit & Payment Cards' },
          ],
        },
        {
          label: 'Real Estate & Moving',
          chilren: [
            { label: 'Storage Companies' },
            { label: 'Land Real Estate' },
            { label: 'Roommate Matching' },
            { label: 'Industrial Real Estate' },
            { label: 'Commercial Real Estate' },
            { label: 'Moving Services' },
            { label: 'Residential Real Estate' },
            { label: 'Property Management' },
            { label: 'Tenant Screening' },
          ],
        },
        {
          label: 'Executive Coaching',
          chilren: [{ label: 'Career Coaching' }, { label: 'Intervention Coaching' }, { label: 'Life Coaching' }, { label: 'Productivity Coaching' }, { label: 'Business & Team Coaching' }],
        },
      ],
    },
    {
      label: 'Places & Travel',
      children: [
        {
          label: 'Cruises',
          children: [
            { label: 'Special Interest Group Cruises' },
            { label: 'Family Cruises' },
            { label: 'Luxury Cruises' },
            { label: 'Adventure Cruises' },
            { label: 'Theme Cruises' },
            { label: 'River Cruises' },
          ],
        },
        {
          label: 'Events',
          children: [
            { label: 'Event Planning' },
            { label: 'Tradeshows' },
            { label: 'Festivals' },
            { label: 'Conventions' },
            { label: 'Conferences' },
            { label: 'Carnivals' },
            { label: 'Concerts' },
            { label: 'Event Ticketing' },
            { label: 'Circuses' },
            { label: 'Weddings' },
          ],
        },
        {
          label: 'Transportation',
          children: [
            { label: 'Airport Transportation' },
            { label: 'Vehicle Rental Services' },
            { label: 'Airlines' },
            { label: 'Parking' },
            { label: 'Ride Sharing Services' },
            { label: 'Public Transportation Services' },
            { label: 'Taxi Services' },
          ],
        },
        {
          label: 'Lodging',
          children: [{ label: 'Hotels' }, { label: 'Hostels' }, { label: 'Bed and Breakfasts' }, { label: 'Lodging Rentals' }, { label: 'Motels' }],
        },
        {
          label: 'Travel Services',
          children: [
            { label: 'Travel Clubs' },
            { label: 'Booking Services' },
            { label: 'Airport Services' },
            { label: 'Passport & Visa Services' },
            { label: 'Travel Guides' },
            { label: 'Travel Insurance' },
            { label: 'Destination Services' },
          ],
        },
        {
          label: 'Entertainment Attractions',
          children: [
            { label: 'Theaters' },
            { label: 'Zoos' },
            { label: 'Casinos' },
            { label: 'Aquariums' },
            { label: 'Amusement Parks' },
            { label: 'Outdoor Attractions' },
            { label: 'Food Tours' },
            { label: 'Museums' },
          ],
        },
        {
          label: 'Luggage & Travel Gear',
          children: [
            { label: 'Travel Accessories' },
            { label: 'Waist Packs' },
            { label: 'Messenger Bags' },
            { label: 'Travel Totes' },
            { label: 'Travel Duffels' },
            { label: 'Laptop Bags' },
            { label: 'Brief Cases' },
            { label: 'Luggage' },
            { label: 'Backpacks' },
          ],
        },
        {
          label: 'Restaurants',
          children: [
            { label: 'Ethnic Restaurants' },
            { label: 'Fine Dining Restaurants' },
            { label: 'Food Trucks' },
            { label: 'Fast Food' },
            { label: 'Cafes' },
            { label: 'Reservation Services' },
            { label: 'Casual Dining' },
            { label: 'Fast Casual Restaurants' },
            { label: 'Bars' },
          ],
        },
        { label: 'Movie Theaters' },
      ],
    },
    {
      label: 'Software',
      children: [
        {
          label: 'Productivity',
          children: [
            { label: 'Remote Computer' },
            { label: 'File Sharing' },
            { label: 'Project Management' },
            { label: 'Document Collaboration' },
            { label: 'Messaging Apps' },
            { label: 'Password Management' },
            { label: 'Visual Collaboration' },
            { label: 'Video Conferencing' },
          ],
        },
        {
          label: 'Business Solutions',
          children: [
            { label: 'Word Processing' },
            { label: 'Online Course Software' },
            { label: 'Database Services' },
            { label: 'Live Chat Software' },
            { label: 'Asset Management' },
            { label: 'Accounting & Bookkeeping' },
            { label: 'Human Resources' },
            { label: 'Knowledge Base Software' },
            { label: 'Presentations' },
            { label: 'CRM' },
          ],
        },
        {
          label: 'Entertainment',
          children: [
            { label: 'Gambling' },
            { label: 'Blogs' },
            { label: 'Movies & TV' },
            { label: 'Social Platforms' },
            { label: 'Audio & Music' },
            { label: 'Shopping' },
            { label: 'News & Magazines' },
            { label: 'Books' },
            { label: 'Gaming' },
          ],
        },
        {
          label: 'Education',
          children: [{ label: 'Professional Development & Skills Training' }, { label: 'K-12 Education' }, { label: 'Higher Education' }, { label: 'Educational Resources' }],
        },
        {
          label: 'Creativity & Design',
          children: [
            { label: 'Video Editing' },
            { label: 'Sculpting & Modeling ' },
            { label: 'Icons' },
            { label: 'Photo Editing' },
            { label: 'Fonts' },
            { label: 'Graphic Design' },
            { label: 'Drawing & Painting' },
          ],
        },
        {
          label: 'Marketing Tools',
          children: [
            { label: 'Promotional Products' },
            { label: 'Affiliate Marketing' },
            { label: 'Advertising Tools' },
            { label: 'Digital Marketing' },
            { label: 'Marketing Collateral' },
            { label: 'Marketing Analytics' },
            { label: 'Market Research' },
          ],
        },
        {
          label: 'Software Utilities',
          children: [{ label: 'Cryptocurrency Mining' }, { label: 'Computer Utilities' }, { label: 'Communication Tools' }],
        },
        {
          label: 'Jobs & Hiring',
          children: [{ label: 'Interview Prep & Coaching' }, { label: 'Job Search' }, { label: 'Resume Services' }],
        },
        {
          label: 'Website Development',
          children: [{ label: 'Website Hosting' }, { label: 'Website Creation Tools' }, { label: 'eCommerce Management Tools' }, { label: 'Website Performance' }],
        },
      ],
    },
  ] as Tree[],
})
</script>
